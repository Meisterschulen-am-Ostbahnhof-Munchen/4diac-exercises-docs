# Training_01_PUBLISH_SUBSCRIBE: Four distributed button→LED couplings over multicast

![Training_01_PUBLISH_SUBSCRIBE_network](./Training_01_PUBLISH_SUBSCRIBE_network.svg)

* * * * * * * * * *

## Introduction

`Training_01_PUBLISH_SUBSCRIBE` is the simplest of the three distribution
exercises under `test_VV/sys/`: four independent button→LED-strip pairs
(green/yellow/red/blue), each distributed over its own `PUBLISH_1`/
`SUBSCRIBE_1` port pair via FORTE's own multicast — **not** OPC-UA (shown
as alternatives for the same basic pattern by `Training_01_CLIENT_SERVER`/
`Training_02_OPC_UA_RES`). `Training_02_OPC_UA_RES` itself references this
multicast precedent as "already shown" for the cross-mapping connection
mechanism.

## Function Blocks Used

| Instance | Location | Type | Purpose |
|---|---|---|---|
| `BUTTON_GREEN`/`_YELLOW`/`_RED`/`_BLUE` | Application (`App_PUBLISH_SUBSCRIBE`) | `logiBUS::io::DI::logiBUS_IX` | Reads `Input_I1`…`I4` |
| `LED_GREEN_5HZ`/`_YELLOW_5HZ`/`_RED_5HZ`/`_BLUE_5HZ` | Application | `logiBUS::io::DO_LED::logiBUS_LED_strip_QX` | Drives `Output_strip` in the respective color, 5 Hz blink rate |
| `PUBLISH_BUTTON_*` | Resource `EMB_RES_PUBLISH` (`FORTE_PC_PUBLISH`) | `iec61499::net::PUBLISH_1` | Sends the button state via multicast, one fixed port per channel (`PORT_0`…`PORT_3`) |
| `SUBSCRIBE_BUTTON_*` | Resource `EMB_RES_SUBSCRIBE` (`FORTE_PC_SUBSCRIBE`) | `iec61499::net::SUBSCRIBE_1` | Receives the matching port, directly drives the corresponding LED |

The Application itself has no networking at all: `BUTTON_*.IND`/`.IN` →
`LED_*.REQ`/`.OUT`, the purely logical base pattern, identical for all four
color channels. Only the `Mapping` splits buttons onto `FORTE_PC_PUBLISH`
and LEDs onto `FORTE_PC_SUBSCRIBE`.

## Program Flow and Connections

1. **Application**: four parallel, fully independent button→LED pairs, no
   data exchange between the color channels.
2. **Mapping**: all four `BUTTON_*` → `FORTE_PC_PUBLISH.EMB_RES_PUBLISH`,
   all four `LED_*_5HZ` → `FORTE_PC_SUBSCRIBE.EMB_RES_SUBSCRIBE`.
3. **Resource `EMB_RES_PUBLISH`**: `App_PUBLISH_SUBSCRIBE.BUTTON_*.IND`
   (dotted-path across the mapping boundary) → `PUBLISH_BUTTON_*.REQ`, plus
   `BUTTON_*.IN` → `PUBLISH_BUTTON_*.SD_1` — each button sends its state on
   its own multicast port (`PORT_0` green … `PORT_3` blue).
4. **Resource `EMB_RES_SUBSCRIBE`**: `SUBSCRIBE_BUTTON_*.IND` →
   `App_PUBLISH_SUBSCRIBE.LED_*_5HZ.REQ`, `SUBSCRIBE_BUTTON_*.RD_1` →
   `LED_*_5HZ.OUT` — mirrored, again a direct dotted-path connection across
   the mapping boundary, no intermediate block.
5. **Runtime**: the actual transfer between `PUBLISH_BUTTON_*` and
   `SUBSCRIBE_BUTTON_*` does not exist as a model connection - it happens
   purely as FORTE multicast over the shared port at runtime.

## Technical Notes

- **Four independent channels, one port per channel**: each button/LED
  pair gets its own `PORT_n` (`VV::const::ports::myPorts`) - no shared
  channel, no risk of the colors getting mixed up.
- **Base pattern for the sibling exercises**: the same button→LED
  structure reappears in `Training_01_CLIENT_SERVER` (request/reply
  instead of multicast) and `Training_02_OPC_UA_RES` (OPC-UA instead of
  FORTE's own network) - here in its simplest, one-way variant.

## Learning Objectives

- Base pattern for FORTE's own publish/subscribe (`PUBLISH_1`/
  `SUBSCRIBE_1`) as the simplest form of distributed IEC 61499
  communication.
- Cross-mapping connection between an Application FB pin and a Resource FB
  pin via a dotted-path reference.
- Multi-channel communication over fixed, per-channel ports.

**Difficulty**: Basic
**Prerequisites**: basics of button→LED coupling, IEC 61499 mapping
concept.

## Summary

`Training_01_PUBLISH_SUBSCRIBE` distributes four independent button→LED
pairs over two devices via FORTE's own multicast, and serves as the
simplest precedent for the two other distribution patterns
(request/reply, OPC-UA) shown in this directory's sibling exercises.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
