# Training_01_CLIENT_SERVER: Button→LED via request/reply with an activation counter

![Training_01_CLIENT_SERVER_network](./Training_01_CLIENT_SERVER_network.svg)

* * * * * * * * * *

## Introduction

`Training_01_CLIENT_SERVER` carries the same four-channel button→LED
pattern from `Training_01_PUBLISH_SUBSCRIBE` over to `CLIENT_1`/`SERVER_1`
(request/reply) instead of `PUBLISH_1`/`SUBSCRIBE_1` (multicast). Unlike
multicast, the server here sends back a genuine reply: it counts how many
times each LED was activated and reports the current count back to the
client.

## Function Blocks Used

| Instance | Location | Type | Purpose |
|---|---|---|---|
| `BUTTON_GREEN`/`_YELLOW`/`_RED`/`_BLUE` | Application (`App_CLIENT_SERVER`) | `logiBUS::io::DI::logiBUS_IX` | Reads `Input_I1`…`I4` |
| `LED_GREEN_5HZ`/`_YELLOW_5HZ`/`_RED_5HZ`/`_BLUE_5HZ` | Application | `logiBUS::io::DO_LED::logiBUS_LED_strip_QX` | Drives `Output_strip`, 5 Hz blink rate |
| `CLIENT_BUTTON_*` | Resource `EMB_RES_CLIENT` (`FORTE_PC_CLIENT`) | `iec61499::net::CLIENT_1` | Sends the activation request, receives the counter value as a reply |
| `UINT2UINT_0`…`_3` | Resource `EMB_RES_CLIENT` | `iec61131::selection::F_MOVE` (UINT) | Accepts the reply (`RD_1`) - a dead end, not used further in the model |
| `SERVER_BUTTON_*` | Resource `EMB_RES_SERVER` (`FORTE_PC_SERVER`) | `iec61499::net::SERVER_1` | Receives the request, drives the LED, sends the counter value back |
| `E_CTU_LED_*_5HZ` | Application, mapped onto `EMB_RES_SERVER` | `iec61499::events::E_CTU` | Counts activations per color (`CU` on every `LED.CNF`) |

## Program Flow and Connections

1. **Application**: four independent button→LED pairs, structurally
   identical to `Training_01_PUBLISH_SUBSCRIBE`; plus one `E_CTU` counter
   per color, reacting to `LED_*_5HZ.CNF`.
2. **Mapping**: all four `BUTTON_*` → `FORTE_PC_CLIENT.EMB_RES_CLIENT`, all
   four `LED_*_5HZ` and `E_CTU_LED_*_5HZ` → `FORTE_PC_SERVER.EMB_RES_SERVER`.
3. **Resource `EMB_RES_CLIENT`**: `App_CLIENT_SERVER.BUTTON_*.IND`
   (dotted-path) → `CLIENT_BUTTON_*.REQ`, `BUTTON_*.IN` →
   `CLIENT_BUTTON_*.SD_1` - the button fires the request together with its
   state, each channel on its own port (`PORT_B0`…`PORT_B3`). The reply
   (`CLIENT_BUTTON_*.CNF` + `.RD_1`) flows into `UINT2UINT_*` - a plain
   endpoint, not processed further in the model.
4. **Resource `EMB_RES_SERVER`**: `SERVER_BUTTON_*.IND` →
   `App_CLIENT_SERVER.LED_*_5HZ.REQ`, `SERVER_BUTTON_*.RD_1` →
   `LED_*_5HZ.OUT` - the request drives the LED directly. Its confirmation
   (`LED_*_5HZ.CNF`) increments `E_CTU_LED_*_5HZ.CU`; its `CUO` event fires
   `SERVER_BUTTON_*.RSP`, carrying the current count (`E_CTU.CV`) as
   `SD_1` - so the actual reply only goes out once the LED has really
   confirmed.

## Technical Notes

- **Genuine, stateful reply instead of a dummy value**: unlike the sibling
  exercise `Training_02_CLIENT_SERVER2` (where the server only sends back
  a constant placeholder), the reply here carries the real, continuously
  updated activation count per channel.
- **Reply only after confirmation**: `SERVER_BUTTON_*.RSP` is not fired
  directly by `SERVER_BUTTON_*.IND`, but only indirectly via
  `LED_*_5HZ.CNF` → `E_CTU.CUO` - so the reply confirms that the LED
  actually reacted, not merely that the request arrived.
- **Client currently discards the reply**: `UINT2UINT_*` (`F_MOVE`) does
  accept the counter value, but has no further output in the model - a
  natural extension point (e.g. displaying the count), not already part of
  this exercise.

## Learning Objectives

- FORTE's own request/reply base pattern (`CLIENT_1`/`SERVER_1`) compared
  to the pure publish/subscribe of `Training_01_PUBLISH_SUBSCRIBE`.
- Decoupling reply data in time from the actual action (reply only after
  confirmation, not immediately on request arrival).
- A counter block (`E_CTU`) as state shared between several events.

**Difficulty**: Intermediate
**Prerequisites**: `Training_01_PUBLISH_SUBSCRIBE` (distributed
button→LED base pattern), IEC 61499 mapping concept.

## Summary

`Training_01_CLIENT_SERVER` shows request/reply as an alternative to
multicast for the same four-channel pattern: the server not only
acknowledges each activation but returns a genuine, running count as the
reply, rather than sending back only a placeholder.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
