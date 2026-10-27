# Training_18_OPC_UA_RES: Objects and Variables via PUBLISH_0 — Two Devices

![Training_18_OPC_UA_RES_network](./Training_18_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_18_OPC_UA_RES` implements the "objects/variables via `PUBLISH_0`" example from `opcua.adoc` on two devices: device B uses four buttons with one [`PUBLISH_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_0/) each to create an OPC-UA object (`Input_I1`), create a variable (`Input_I2`), delete the variable (`Input_I3`) and delete the object (`Input_I4`). Device A reads the variable from device B with [`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/) when `Input_I1` is pressed.

The training is the two-device counterpart of `L18` in `test_AX/Meins/OPC_UA_Lokal`.

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_18_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `READ_VAR` ([`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/): `QI` = `TRUE`, `ID` = `T18_VAR_CLIENT_READ`), `VAR_TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_18_network.svg) | B (`192.168.1.12`) | For each button a pair of `TASTER_Ix` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_Ix`, `InputEvent` = `BUTTON_PRESS_DOWN`) and [`PUBLISH_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_0/) (`QI` = `TRUE`): `OBJ_CREATE` (`I1`, `ID` = `T18_OBJ_CREATE`), `VAR_CREATE` (`I2`, `ID` = `T18_VAR_CREATE`), `VAR_DELETE` (`I3`, `ID` = `T18_VAR_DELETE`), `OBJ_DELETE` (`I4`, `ID` = `T18_OBJ_DELETE`) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants | Node | Meaning |
|---|---|---|
| `T18_OBJ_CREATE` | `opc_ua[CREATE_OBJECT;,i=58;/Objects/1:T18_MyObject]` | B creates the object `T18_MyObject` (type `BaseObjectType`, `i=58`) |
| `T18_VAR_CREATE` | `opc_ua[CREATE_VARIABLE;,i=63;,i=6;/Objects/1:T18_MyVariable]` | B creates the variable `T18_MyVariable` (`BaseDataVariableType`, `i=63`; data type `Int32`, `i=6`) |
| `T18_VAR_DELETE` | `opc_ua[DELETE_VARIABLE;/Objects/1:T18_MyVariable]` | B deletes the variable |
| `T18_OBJ_DELETE` | `opc_ua[DELETE_OBJECT;/Objects/1:T18_MyObject]` | B deletes the object |
| `T18_VAR_CLIENT_READ` | `/Objects/1:T18_MyVariable` | Node of the created variable on B; A reads it via `CLIENT` (`READ` with B's endpoint) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A), event connections:

    1. `TASTER_I1.IND` → `READ_VAR.REQ`
    2. `TASTER_I1.INITO` → `READ_VAR.INIT`
    3. `READ_VAR.CNF` → `VAR_TO_INT.REQ`

2. **`SubApp_PC_A`** (device A), data connection:

    1. `READ_VAR.RD_1` → `VAR_TO_INT.IN`

3. **`SubApp_PC_B`** (device B), event connections (each button with its own `PUBLISH_0`):

    1. `TASTER_I1.IND` → `OBJ_CREATE.REQ`, `TASTER_I1.INITO` → `OBJ_CREATE.INIT`
    2. `TASTER_I2.IND` → `VAR_CREATE.REQ`, `TASTER_I2.INITO` → `VAR_CREATE.INIT`
    3. `TASTER_I3.IND` → `VAR_DELETE.REQ`, `TASTER_I3.INITO` → `VAR_DELETE.INIT`
    4. `TASTER_I4.IND` → `OBJ_DELETE.REQ`, `TASTER_I4.INITO` → `OBJ_DELETE.INIT`

## Technical Notes

- **The ID is the command**: with `PUBLISH_0` the constants contain the complete action in the format `opc_ua[ACTION;...;path]` (`CREATE_OBJECT`, `CREATE_VARIABLE`, `DELETE_VARIABLE`, `DELETE_OBJECT`) and are used directly as `ID`; they are not computed with `F_OPC_LOCAL`/`F_OPC_REMOTE` (see [OPC-UA addresses](../OPC_UA_Adressen.md)). The paths contain the namespace index `1:`.
- **No data connection on B**: `PUBLISH_0` transfers no data; each button only triggers its `REQ`.
- **Reading on A**: the node of the variable (`T18_VAR_NODE`) is addressed with `F_OPC_REMOTE` (`READ`, B's endpoint). The value read is converted with `F_DINT_TO_INT`.
- **SubApp comment of A**: the comment of `SubApp_PC_A` is the same as that of `SubApp_PC_B` (I1 … I4 for creating/deleting); in the SubApp of A only reading via `Input_I1` is wired.

## Note

- **Not tested on the device.** `Training_13_OPC_UA_RES` through `Training_18_OPC_UA_RES` have not yet been run on a device. This page describes only what is contained in `test_Training_18_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` and `myOpcUaAddresses.gcf`; whether the examples run as described has not been verified.
- Initialization comes from the resources: in `EMB_RES_A` and `EMB_RES_B`, `START.COLD` feeds `E_TRIG` (`EVENTTYPE = 'EInit'`), which triggers the initialization (`EInit`) of the application. That is why the first block of each chain has no connection of its own to its `INIT` input in the SubApp.

## Learning Objectives

- Creating and deleting objects and variables in the OPC-UA address space of a device with `PUBLISH_0`.
- Reading a variable created this way from another device with `CLIENT_0_1`.

## Summary

`Training_18_OPC_UA_RES` is the `PUBLISH_0` example from `opcua.adoc` on two devices: B creates and deletes object and variable via `Input_I1`…`Input_I4`, A reads the variable via `Input_I1`.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
