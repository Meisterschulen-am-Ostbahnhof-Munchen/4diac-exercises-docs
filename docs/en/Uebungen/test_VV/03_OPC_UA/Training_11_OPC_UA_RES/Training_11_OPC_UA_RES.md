# Training_11_OPC_UA_RES: Up/Down Switch over OPC-UA — VT SoftKeys with Feedback

![Training_11_OPC_UA_RES_network](./Training_11_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

Compared to `Training_10_OPC_UA_RES`, `Training_11_OPC_UA_RES` replaces the physical `logiBUS_IXA` inputs with two VT SoftKeys ([`Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/), `SoftKey_F1` = up, `SoftKey_F2` = down). Device A actively writes them via OPC-UA (`CLIENT_1_0`) to the nodes monitored by device B. Device B is identical to Training_10 (2x `AX_SUBSCRIBE_1` → `ILOCK_SWITCH_PROTECT_AX` → 2x `logiBUS_QXA`) and additionally writes the state actually reached (`UP_OUT`/`DOWN_OUT` after arbitration/protection time) back to device A via `AX_SPLIT_2` + `AX_CLIENT_1_0`.

On device A, one [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) per key colors the respective SoftKey according to this reported state — not according to the raw key press. `Softkey_IXA` reads continuously (like `logiBUS_IXA`), so no REQ polling is needed; `INIT` (`EInit`) starts the monitoring automatically.

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_11_network.svg) | A (`192.168.1.11`) | 2x [`Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/) (`UpKey`: `SoftKey_F1`; `DownKey`: `SoftKey_F2`), 2x [`AX_CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_CLIENT_1_0/) (write), 2x [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) (read feedback), 2x [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) (`BG_Up`, `BG_Down`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_11_network.svg) | B (`192.168.1.12`) | 2x [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/), [`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/), [`E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/), 2x [`AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/), 2x [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/), 2x [`AX_CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_CLIENT_1_0/) (write feedback) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants (write to other station / read locally) | Node | Meaning |
|---|---|---|
| `SW1_UP_REMOTE_WRITE / SW1_UP_LOCAL_READ` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Node on B; A writes, B reads locally |
| `SW1_DOWN_REMOTE_WRITE / SW1_DOWN_LOCAL_READ` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Node on B; A writes, B reads locally |
| `SW1_UP_STATE_REMOTE_WRITE / SW1_UP_STATE_LOCAL_READ` | `/Objects/Switch1/UpState,1:s=Switch1_UpState` | Node on A; B writes, A reads locally |
| `SW1_DOWN_STATE_REMOTE_WRITE / SW1_DOWN_STATE_LOCAL_READ` | `/Objects/Switch1/DownState,1:s=Switch1_DownState` | Node on A; B writes, A reads locally |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A), adapter connections:

    1. `UpKey.IN` → `UpTasterClient.IN`
    2. `DownKey.IN` → `DownTasterClient.IN`
    3. `StateUpSubscribe.OUT` → `BG_Up.DI1`
    4. `StateDownSubscribe.OUT` → `BG_Down.DI1`

2. **`SubApp_PC_B`** (device B), adapter connections:

    1. `SUBSCRIBE_UP.OUT` → `ILOCK_AX.UP_IN`
    2. `SUBSCRIBE_DOWN.OUT` → `ILOCK_AX.DOWN_IN`
    3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`
    4. `ILOCK_AX.UP_OUT` → `SPLIT_UP.IN`
    5. `SPLIT_UP.OUT1` → `DigitalOutput_Q7.OUT`
    6. `SPLIT_UP.OUT2` → `StateUpClient.IN`
    7. `ILOCK_AX.DOWN_OUT` → `SPLIT_DOWN.IN`
    8. `SPLIT_DOWN.OUT1` → `DigitalOutput_Q8.OUT`
    9. `SPLIT_DOWN.OUT2` → `StateDownClient.IN`

**Parameters**: Device A: `UpKey`/`DownKey` ([`Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/)): `QI` = `TRUE`, `u16ObjId` = `SoftKey_F1` and `SoftKey_F2` respectively; `UpTasterClient`/`DownTasterClient` ([`AX_CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_CLIENT_1_0/)): `QI` = `TRUE`, `ID` = `SW1_UP_REMOTE_WRITE` and `SW1_DOWN_REMOTE_WRITE` respectively; `StateUpSubscribe`/`StateDownSubscribe` ([`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/)): `QI` = `TRUE`, `ID` = `SW1_UP_STATE_LOCAL_READ` and `SW1_DOWN_STATE_LOCAL_READ` respectively; `BG_Up`/`BG_Down` ([`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/)): `u16ObjId` = `SoftKey_F1` and `SoftKey_F2` respectively. Device B: `SUBSCRIBE_UP`/`SUBSCRIBE_DOWN`: `QI` = `TRUE`, `ID` = `SW1_UP_LOCAL_READ` and `SW1_DOWN_LOCAL_READ` respectively; `ILOCK_AX`: `DT_PROTECT` = `T#1s`; `DigitalOutput_Q7`/`DigitalOutput_Q8`: `QI` = `TRUE`, `Output` = `Output_Q7` and `Output_Q8` respectively; `StateUpClient`/`StateDownClient`: `QI` = `TRUE`, `ID` = `SW1_UP_STATE_REMOTE_WRITE` and `SW1_DOWN_STATE_REMOTE_WRITE` respectively.

## Technical Notes

- **Feedback instead of key press**: the SoftKeys are colored according to the state device B actually reached (after ILOCK arbitration/protection time), not according to the raw key press.
- **State write from B to A**: same principle as the model `SoftKeySR_PC_A_OPC` — device B writes via `AX_CLIENT_1_0` to a node monitored locally by device A (`*_STATE_*`).
- **Fan-out via `AX_SPLIT_2`**: the interlocked state goes in parallel to the physical output and to the feedback client.
- **Continuous reading**: `Softkey_IXA` needs no REQ polling (model `Softkey_IXA_TO_logiBUS_QXA_BG`).

## Learning Objectives

- VT SoftKeys (`Softkey_IXA`) as OPC-UA input instead of physical inputs.
- Writing the confirmed state back (B → A) and displaying it with `GreenWhiteBackground1_AX`.
- Fanning the interlock output out to output and feedback with `AX_SPLIT_2`.

**Difficulty**: Advanced  
**Recommended sequence**: `Training_10_OPC_UA_RES`.

## Summary

`Training_11_OPC_UA_RES` extends Training_10 with VT SoftKeys and feedback: device B reports the state actually reached after interlock and protection time back to device A, where it colors the SoftKey.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
