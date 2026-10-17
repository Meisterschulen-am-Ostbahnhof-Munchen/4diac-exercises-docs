# Training_12_OPC_UA_RES: Up/Down Switch over OPC-UA — Bundled A2X Transport Layer

![Training_12_OPC_UA_RES_network](./Training_12_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_12_OPC_UA_RES` is functionally equivalent to `Training_11_OPC_UA_RES` but bundles the transport layer: instead of 2x `AX_SUBSCRIBE_1`/`AX_CLIENT_1_0` each (4 individual network blocks, 4 constants), **one** block handles the complete roundtrip for up and down (one `CLIENT_2_0`/`SUBSCRIBE_2` each, 2 values per call, 2 constants).

- Device A: the 4 local blocks (2 SoftKeys + 2 `GreenWhiteBackground`) are bundled in [`Softkey2_TO_A2X2_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey2_TO_A2X2_BG/), which exposes only a single `A2X2` plug to the outside; this is plugged directly into [`A2X2_CLIENT_2_0_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2/).
- Device B: [`A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG/) handles the roundtrip. [`A2X2_ILOCK_SWITCH_PROTECT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X2_ILOCK_SWITCH_PROTECT/) encapsulates bridge (`A2X2_TO_A2X`), interlock ([`ILOCK_SWITCH_PROTECT_A2X`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_A2X/)) and split (`A2X_SPLIT_2`) in one composite (`DT_PROTECT` as a parameter) and delivers the interlocked state as an `A2X` plug. [`A2X_TO_QXA2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X_TO_QXA2/) unbundles onto the 2 physical `logiBUS_QXA` outputs.

The data therefore stays `A2X`-bundled until immediately before the physical hardware.

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_12_network.svg) | A (`192.168.1.11`) | `Keys` ([`Softkey2_TO_A2X2_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey2_TO_A2X2_BG/): `u16ObjId_UP` = `SoftKey_F1`, `u16ObjId_DOWN` = `SoftKey_F2`), `NET` ([`A2X2_CLIENT_2_0_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2/): `QI` = `TRUE`, `ID_WRITE` = `SW2_PRESS_REMOTE_WRITE`, `ID_READ` = `SW2_STATE_LOCAL_READ`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_12_network.svg) | B (`192.168.1.12`) | `NET` ([`A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG/): `QI` = `TRUE`, `ID_WRITE` = `SW2_STATE_REMOTE_WRITE`, `ID_READ` = `SW2_PRESS_LOCAL_READ`), `ILOCK` ([`A2X2_ILOCK_SWITCH_PROTECT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X2_ILOCK_SWITCH_PROTECT/): `DT_PROTECT` = `T#1s`), `Outputs` ([`A2X_TO_QXA2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X_TO_QXA2/): `Output_UP` = `Output_Q7`, `Output_DOWN` = `Output_Q8`) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants (write to other station / read locally) | Node | Meaning |
|---|---|---|
| `SW2_PRESS_REMOTE_WRITE / SW2_PRESS_LOCAL_READ` | `/Objects/Switch2/Up,1:s=Switch2_Up;/Objects/Switch2/Down,1:s=Switch2_Down` | Two nodes in one string (`;`), on B; A writes, B reads locally |
| `SW2_STATE_REMOTE_WRITE / SW2_STATE_LOCAL_READ` | `/Objects/Switch2/UpState,1:s=Switch2_UpState;/Objects/Switch2/DownState,1:s=Switch2_DownState` | Two nodes in one string (`;`), on A; B writes, A reads locally |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A), adapter connection:

    1. `Keys.OUT` → `NET.IO`

2. **`SubApp_PC_B`** (device B), adapter connections:

    1. `NET.IO` → `ILOCK.IO`
    2. `ILOCK.OUT` → `Outputs.IN`

## Technical Notes

- **Two constants instead of four**: `SW2_*` bundles two nodes in one string (separated by `;`) for `SUBSCRIBE_2`/`CLIENT_2_0` (see [OPC-UA addresses](../OPC_UA_Adressen.md)).
- **A2X bundling up to the hardware**: on device B the chain bridge → interlock → split stays encapsulated in `A2X2_ILOCK_SWITCH_PROTECT`; only `A2X_TO_QXA2` unbundles onto the physical outputs.
- **`SUBSCRIBE_2` instead of a method call**: a plain OPC-UA method call (`CLIENT_2`/`SERVER_2`, request/response) was rejected for the bundled roundtrip — the remote state can change from a third source independent of our own writes, and the local UI has to react to that push, which only `SUBSCRIBE_2` provides (rationale in commit `04b465d77`).
- **Plug and socket**: device A uses the socket block `A2X2_CLIENT_2_0_SUBSCRIBE_2`, device B the plug variant `A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG`.

## Learning Objectives

- Bundling several values per OPC-UA call (`A2X2_CLIENT_2_0_SUBSCRIBE_2`, 2 constants instead of 4).
- Using bundling composites (`Softkey2_TO_A2X2_BG`, `A2X2_ILOCK_SWITCH_PROTECT`, `A2X_TO_QXA2`) and the A2X variant of the interlock (`ILOCK_SWITCH_PROTECT_A2X`).
- Understanding the plug/socket variants of the network block.

**Difficulty**: Advanced  
**Prerequisites**: `Training_11_OPC_UA_RES`.

## Summary

`Training_12_OPC_UA_RES` is the bundled variant of Training_11: the same function, but a single `A2X2` roundtrip per device, `A2X`-bundled up to just before the SoftKeys and physical outputs respectively.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
