# Training_15_OPC_UA_RES: Method AddValues over OPC-UA — Two Devices

![Training_15_OPC_UA_RES_network](./Training_15_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_15_OPC_UA_RES` implements the `AddValues` method example from `opcua.adoc` on two devices: device B creates the OPC-UA method `AddValues` with [`SERVER_1_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SERVER_1_2/) (two input values, one output value) and computes the sum with [`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/). Device A calls the method with [`CLIENT_2_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_2_1/) when `Input_I1` is pressed, using the values 43 and 10 (result 53 according to the SubApp comment).

The training is the two-device counterpart of `L15` in `test_AX/Meins/OPC_UA_Lokal`.

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_15_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `ADD_CALL` ([`CLIENT_2_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_2_1/): `QI` = `TRUE`, `ID` = `T15_ADD_CALL`, `SD_1` = `INT#43`, `SD_2` = `INT#10`), `RESULT_TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_15_network.svg) | B (`192.168.1.12`) | `ADD_VALUES` ([`SERVER_1_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SERVER_1_2/): `QI` = `TRUE`, `ID` = `T15_ADD_METHOD`), `TO_DINT_1`, `TO_DINT_2` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/)), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants | Node | Meaning |
|---|---|---|
| `T15_ADD_METHOD / T15_ADD_CALL` | `/Objects/T15/AddValues` | Method on B; B creates it locally (`CREATE_METHOD`), A calls it with B's endpoint (`CALL_METHOD`) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A), event connections:

    1. `TASTER_I1.IND` → `ADD_CALL.REQ`
    2. `TASTER_I1.INITO` → `ADD_CALL.INIT`
    3. `ADD_CALL.CNF` → `RESULT_TO_DINT.REQ`

2. **`SubApp_PC_A`** (device A), data connection:

    1. `ADD_CALL.RD_1` → `RESULT_TO_DINT.IN`

3. **`SubApp_PC_B`** (device B), event connections:

    1. `ADD_VALUES.IND` → `TO_DINT_1.REQ`
    2. `TO_DINT_1.CNF` → `TO_DINT_2.REQ`
    3. `TO_DINT_2.CNF` → `F_ADD.REQ`
    4. `F_ADD.CNF` → `TO_INT.REQ`
    5. `TO_INT.CNF` → `ADD_VALUES.RSP`

4. **`SubApp_PC_B`** (device B), data connections:

    1. `ADD_VALUES.RD_1` → `TO_DINT_1.IN`
    2. `ADD_VALUES.RD_2` → `TO_DINT_2.IN`
    3. `TO_DINT_1.OUT` → `F_ADD.IN1`
    4. `TO_DINT_2.OUT` → `F_ADD.IN2`
    5. `F_ADD.OUT` → `TO_INT.IN`
    6. `TO_INT.OUT` → `ADD_VALUES.SD_1`

## Technical Notes

- **Request/response**: `SERVER_1_2` signals the call with `IND` and returns the result only after `RSP`; the calculation chain runs in between (`IND` → … → `RSP`).
- **Address without node ID**: the node path `/Objects/T15/AddValues` has no `,1:s=...` suffix (unlike the variable nodes).
- **Call values**: `ADD_CALL` sends the values `SD_1` = `INT#43` and `SD_2` = `INT#10` that are set on the block itself.

## Note

- **Not tested on the device.** `Training_13_OPC_UA_RES` through `Training_18_OPC_UA_RES` have not yet been run on a device. This page describes only what is contained in `test_Training_15_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` and `myOpcUaAddresses.gcf`; whether the examples run as described has not been verified.
- Initialization comes from the resources: in `EMB_RES_A` and `EMB_RES_B`, `START.COLD` feeds `E_TRIG` (`EVENTTYPE = 'EInit'`), which triggers the initialization (`EInit`) of the application. That is why the first block of each chain has no connection of its own to its `INIT` input in the SubApp.
- Note: Every `SERVER_0` needs `IND` → `RSP` directly on its own block, otherwise all OPC-UA publishes of the station stall for up to 4 s (rule see [SERVER_0](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SERVER_0/)). For methods with a return value (`SERVER_1_2`), `RSP` comes only after the immediate calculation.

## Learning Objectives

- Creating an OPC-UA method with `SERVER_1_2` on one device and calling it with `CLIENT_2_1` from another device.
- Wiring two input values and one output value of a method.

## Summary

`Training_15_OPC_UA_RES` is the `AddValues` method example from `opcua.adoc` on two devices: B provides the method, A calls it via `Input_I1` with 43 and 10.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
