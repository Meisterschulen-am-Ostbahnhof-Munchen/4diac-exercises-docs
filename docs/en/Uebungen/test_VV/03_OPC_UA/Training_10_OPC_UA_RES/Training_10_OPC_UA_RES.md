# Training_10_OPC_UA_RES: Up/Down Switch over OPC-UA — Two Pushbuttons with Interlock and Protection Time

![Training_10_OPC_UA_RES_network](./Training_10_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_10_OPC_UA_RES` is a distributed up/down circuit: device A reads the two physical pushbuttons `Input_I1` (up) and `Input_I2` (down) and actively writes both via OPC-UA (`CLIENT_1_0`) to the nodes monitored by device B. Device B merges both states in [`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/) (last-wins with protection time) and switches `Output_Q7` and `Output_Q8`. Both buttons are momentary (spring-return); releasing reports itself as a separate FALSE event via `logiBUS_IXA`.

The protocol lives in the composite (`SUB style`), not in the device's resource. This is the first exercise of the three-part series Training_10–12 (Training_11 replaces the buttons with VT SoftKeys, Training_12 bundles the transport layer).

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_10_network.svg) | A (`192.168.1.11`) | Two instances of [`logiBUS_IXA_TO_CLIENT_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_CLIENT_OPC/) (`UpTaster`: `Input` = `Input_I1`, `ID` = `SW1_UP_REMOTE_WRITE`; `DownTaster`: `Input` = `Input_I2`, `ID` = `SW1_DOWN_REMOTE_WRITE`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_10_network.svg) | B (`192.168.1.12`) | 2x [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/), [`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/), [`E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/), 2x [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants (write to other station / read locally) | Node | Meaning |
|---|---|---|
| `SW1_UP_REMOTE_WRITE / SW1_UP_LOCAL_READ` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Node on B; A writes (`WRITE`), B reads locally (`READ`) |
| `SW1_DOWN_REMOTE_WRITE / SW1_DOWN_LOCAL_READ` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Node on B; A writes (`WRITE`), B reads locally (`READ`) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A): `UpTaster` reads `Input_I1`, `DownTaster` reads `Input_I2`; both ([`logiBUS_IXA_TO_CLIENT_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_CLIENT_OPC/)) write the state via OPC-UA `WRITE` to the node on device B (`SW1_UP_REMOTE_WRITE` and `SW1_DOWN_REMOTE_WRITE` respectively). There are no connections inside the SubApp.
2. **`SubApp_PC_B`** (device B), adapter connections:

    1. `SUBSCRIBE_UP.OUT` → `ILOCK_AX.UP_IN`
    2. `SUBSCRIBE_DOWN.OUT` → `ILOCK_AX.DOWN_IN`
    3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`
    4. `ILOCK_AX.UP_OUT` → `DigitalOutput_Q7.OUT`
    5. `ILOCK_AX.DOWN_OUT` → `DigitalOutput_Q8.OUT`

**Parameters**: `SUBSCRIBE_UP`/`SUBSCRIBE_DOWN` ([`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/)): `QI` = `TRUE`, `ID` = `SW1_UP_LOCAL_READ` and `SW1_DOWN_LOCAL_READ` respectively; `ILOCK_AX` ([`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/)): `DT_PROTECT` = `T#1s`; `DigitalOutput_Q7`/`DigitalOutput_Q8` ([`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)): `QI` = `TRUE`, `Output` = `Output_Q7` and `Output_Q8` respectively.

## Technical Notes

- **No intermediate logic needed**: `AX_SUBSCRIBE_1.OUT` already has the adapter type `AX` — the same type that `ILOCK_SWITCH_PROTECT_AX.UP_IN`/`.DOWN_IN` expect as sockets.
- **Last-wins with protection time**: `ILOCK_SWITCH_PROTECT_AX` arbitrates up and down; `DT_PROTECT` = `T#1s` sets the protection time, `E_TimeOut` provides the timer via the `timeOut` socket.
- **FB network directly in the composite**: the combination (2x subscribe + interlock + protection timer + 2x output) is exercise-specific and therefore sits directly in `SubApp_PC_B`.
- **Momentary buttons**: releasing a button is reported as a separate FALSE event via `logiBUS_IXA`.

## Learning Objectives

- Distributed up/down circuit: device A writes actively (`CLIENT_1_0`), device B monitors locally (`AX_SUBSCRIBE_1`).
- Feeding two adapter sockets (`AX`) directly into `ILOCK_SWITCH_PROTECT_AX` (last-wins, protection time `DT_PROTECT`).
- The "SUB style" pattern: protocol in the composite rather than in the resource.

**Difficulty**: Advanced  
**Prerequisites**: `Training_09_OPC_UA_RES`.

## Summary

`Training_10_OPC_UA_RES` shows the basic form of the series: two physical momentary buttons on device A, individual network blocks (`AX_CLIENT_1_0` inside the composites, 2x `AX_SUBSCRIBE_1`) and the interlock with protection time on device B.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
