# Training_02_CLIENT_SERVER2: Reduced request/reply with plain digital I/O

![Training_02_CLIENT_SERVER2_network](./Training_02_CLIENT_SERVER2_network.svg)

* * * * * * * * * *

## Introduction

`Training_02_CLIENT_SERVER2` is a reduced variant of
`Training_01_CLIENT_SERVER`: the same four-channel request/reply structure
(`CLIENT_1`/`SERVER_1`), but with plain digital inputs/outputs
(`logiBUS_IX`/`logiBUS_QX`) instead of buttons with blinking LED strips,
and without an activation counter on the server side - the reply here is a
fixed placeholder value (`DINT#0`), not the genuine counter value from
`Training_01_CLIENT_SERVER`. This shows the request/reply protocol in its
minimal form, without the added counter logic.

## Function Blocks Used

| Instance | Location | Type | Purpose |
|---|---|---|---|
| `Input_I1`…`_I4` | Application (`App_CLIENT_SERVER`) | `logiBUS::io::DI::logiBUS_IX` | Reads `Input_I1`…`I4` |
| `Output_Q1`…`_Q4` | Application | `logiBUS::io::DQ::logiBUS_QX` | Drives `Output_Q1`…`Q4` |
| `CLIENT_BUTTON_*` | Resource `EMB_RES_CLIENT` (`FORTE_PC_CLIENT`) | `iec61499::net::CLIENT_1` | Sends the request together with the input state |
| `UINT2UINT_0`…`_3` | Resource `EMB_RES_CLIENT` | `iec61131::selection::F_MOVE` (UINT) | Accepts the reply - a dead end, not used further |
| `SERVER_BUTTON_*` | Resource `EMB_RES_SERVER` (`FORTE_PC_SERVER`) | `iec61499::net::SERVER_1` | Receives the request, drives the output directly, replies with `DINT#0` |

## Program Flow and Connections

1. **Application**: four independent `Input_I*`→`Output_Q*` pairs, no
   counter or blink logic - the simplest possible I/O base pattern.
2. **Mapping**: all four `Input_I*` → `FORTE_PC_CLIENT.EMB_RES_CLIENT`, all
   four `Output_Q*` → `FORTE_PC_SERVER.EMB_RES_SERVER`.
3. **Resource `EMB_RES_CLIENT`**: `Input_I*.IND`/`.IN` (dotted-path) →
   `CLIENT_BUTTON_*.REQ`/`.SD_1`, each channel on its own port
   (`PORT_B0`…`PORT_B3`). The reply flows into `UINT2UINT_*` just like in
   `Training_01_CLIENT_SERVER` - the same pattern carried over unchanged,
   still a dead end with no further processing.
4. **Resource `EMB_RES_SERVER`**: `SERVER_BUTTON_*.IND`/`.RD_1` →
   `Output_Q*.REQ`/`.OUT` - **directly**, with no counter and no detour via
   a confirmation. `SERVER_BUTTON_*` has its `SD_1` parameter fixed at
   `DINT#0`: the reply always carries the same placeholder value,
   regardless of the actual switching action.

## Technical Notes

- **Static instead of counted reply**: the `SD_1="DINT#0"` parameter on
  `SERVER_BUTTON_*` replaces the `E_CTU` counting pattern from
  `Training_01_CLIENT_SERVER` - the reply no longer carries genuine
  information, only the protocol acknowledgement itself matters.
- **No confirmation chain**: unlike `Training_01_CLIENT_SERVER` (reply
  only after `LED.CNF`), here `SERVER_BUTTON_*.IND` doesn't trigger the
  reply indirectly at all - the output is switched immediately, with no
  intermediate step.
- **Same client-side pattern as `Training_01_CLIENT_SERVER`**: the
  `UINT2UINT_*` blocks (dead-end reply intake) are carried over unchanged -
  the simplification only touches the server side and the I/O blocks.

## Learning Objectives

- The request/reply protocol (`CLIENT_1`/`SERVER_1`) in its minimal form,
  without additional state/counting logic.
- The difference between a reply that carries real meaning
  (`Training_01_CLIENT_SERVER`) and a bare protocol acknowledgement with a
  placeholder value.

**Difficulty**: Basic
**Prerequisites**: `Training_01_CLIENT_SERVER` (complete request/reply
pattern with counter) as a point of comparison.

## Summary

`Training_02_CLIENT_SERVER2` strips `Training_01_CLIENT_SERVER` down to
the essentials: plain digital I/O channels, direct output switching with
no detour, and a reply that serves only as a placeholder rather than
carrying a genuine counter value.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
