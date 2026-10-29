# Uebung_OPC_L12_AX: Up/Down Switch Locally — Bundled A2X Transport Layer

![Uebung_OPC_L12_AX_network](./Uebung_OPC_L12_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L12_AX` is functionally equivalent to `Uebung_OPC_L11_AX`, but bundles the transport layer (local variant of `Training_12_OPC_UA_RES`): instead of 2x `AX_PUBLISH_1`/`AX_SUBSCRIBE_1` each, **one** block handles the complete local round trip for up and down (one `PUBLISH_2`/`SUBSCRIBE_2` each, 2 values per call, 2 constants). `A2X2_PUBLISH_2_SUBSCRIBE_2` and `A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG` replace the remote `CLIENT_2_0` blocks of the template. Everything runs on **one** device without endpoint.

- The 4 local blocks (2 SoftKeys + 2 `GreenWhiteBackground`) are bundled in `Keys` (`Softkey2_TO_A2X2_BG`), which shows only one `A2X2` plug to the outside; this is plugged directly into `NET`.
- `B_NET` (plug variant) handles the way back; `ILOCK` encapsulates bridge, interlock and split; `Outputs` unbundles to the 2 physical outputs.

Overview of all local exercises: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Function Blocks (FBs) Used

| Name | Type | Parameters | Explanation |
|---|---|---|---|
| `Keys` | [`Softkey2_TO_A2X2_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey2_TO_A2X2_BG/) | `u16ObjId_UP` = `SoftKey_F1`, `u16ObjId_DOWN` = `SoftKey_F2` | 2 SoftKeys and 2 `GreenWhiteBackground` bundled on one bidirectional `A2X2` adapter output. |
| `NET` | [`A2X2_PUBLISH_2_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/A2X2_PUBLISH_2_SUBSCRIBE_2/) | `QI` = `TRUE`, `ID_WRITE` = `SW2_PRESS_WRITE`, `ID_READ` = `SW2_STATE_READ` | Socket variant: writes both actuations locally via `PUBLISH_2` and reads both states locally via `SUBSCRIBE_2`. |
| `B_NET` | [`A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG/) | `QI` = `TRUE`, `ID_WRITE` = `SW2_STATE_WRITE`, `ID_READ` = `SW2_PRESS_READ` | Plug variant: reads both actuations locally via `SUBSCRIBE_2` and writes both states locally via `PUBLISH_2`. |
| `ILOCK` | [`A2X2_ILOCK_SWITCH_PROTECT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X2_ILOCK_SWITCH_PROTECT/) | `DT_PROTECT` = `T#1s` | Encapsulates bridge (`A2X2_TO_A2X`), interlock (`ILOCK_SWITCH_PROTECT_A2X`) and split (`A2X_SPLIT_2`) with protection dead time; delivers the interlocked state as `A2X` plug. |
| `Outputs` | [`A2X_TO_QXA2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X_TO_QXA2/) | `Output_UP` = `Output_Q7`, `Output_DOWN` = `Output_Q8` | Unbundles the `A2X` pair onto the 2 physical `logiBUS_QXA` outputs. |

## OPC-UA Address Space

The IDs come from `const\OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`) and are computed with the functions of the `MyLib_OPC` lib (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Constant | Action | Node / method | Meaning |
|---|---|---|---|
| `SW2_PRESS_WRITE` / `SW2_PRESS_READ` | `WRITE` / `READ` | `/Objects/Switch2/Up,1:s=Switch2_Up;/Objects/Switch2/Down,1:s=Switch2_Down` | Two nodes in one string (`;`): up/down actuation |
| `SW2_STATE_WRITE` / `SW2_STATE_READ` | `WRITE` / `READ` | `/Objects/Switch2/UpState,1:s=Switch2_UpState;/Objects/Switch2/DownState,1:s=Switch2_DownState` | Two nodes in one string (`;`): up/down reached |

## Program Flow and Connections

Adapter connections:

1. `Keys.OUT` → `NET.IO`
2. `B_NET.IO` → `ILOCK.IO`
3. `ILOCK.OUT` → `Outputs.IN`

## Technical Notes

- **Two constants instead of four**: `SW2_*` bundles two nodes in one string (separated by `;`) for `SUBSCRIBE_2`/`PUBLISH_2`.
- **A2X bundling up to the hardware**: the chain bridge → interlock → split stays encapsulated in `A2X2_ILOCK_SWITCH_PROTECT`; only `A2X_TO_QXA2` unbundles onto the physical outputs.
- **Plug and socket**: `NET` is the socket variant, `B_NET` the plug variant of the network block.

## Learning Objectives

- Bundle several values per OPC-UA call (`A2X2_PUBLISH_2_SUBSCRIBE_2`, 2 constants instead of 4).
- Use bundling composites (`Softkey2_TO_A2X2_BG`, `A2X2_ILOCK_SWITCH_PROTECT`, `A2X_TO_QXA2`).
- Understand the difference between plug and socket variant of the network block.

**Difficulty**: Advanced  
**Recommended sequence**: `Uebung_OPC_L11_AX`, `Training_12_OPC_UA_RES` (template, two devices)

## Note / Result

- **Not tested on the device.** So far only L01, L03 and L19 have been tested on `FORTE_PC_AX`; `Uebung_OPC_L12_AX` has not yet been run on the device.
- Locally (without endpoint) `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` and `PUBLISH_0` work. `CALL_METHOD` and the client subscription, in contrast, run with an endpoint to the own station (`localhost` or own IP; both count as remote).

## Summary

`Uebung_OPC_L12_AX` is the bundled variant of L11: same function, but a single `A2X2` round trip (locally via `PUBLISH_2`/`SUBSCRIBE_2`), `A2X`-bundled up to immediately before the SoftKeys and physical outputs.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
