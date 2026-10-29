# Training_14_OPC_UA_RES: Adder over OPC-UA — Two Devices

![Training_14_OPC_UA_RES_network](./Training_14_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_14_OPC_UA_RES` implements the adder example from `opcua.adoc` on device B: B subscribes to the local nodes `Num1` and `Num2`, adds the two values with [`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/) and publishes the sum locally to the node `Result`. According to the SubApp comment, the values are set e.g. with UaExpert. The SubApp of device A is empty.

The training is the two-device counterpart of `L14` in `test_AX/Meins/OPC_UA_Lokal`.

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_14_network.svg) | A (`192.168.1.11`) | empty (no blocks, no connections) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_14_network.svg) | B (`192.168.1.12`) | `NUM1` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T14_NUM1_READ`), `NUM2` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T14_NUM2_READ`), `TO_DINT_1`, `TO_DINT_2` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/)), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)), `RESULT` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T14_RESULT_WRITE`) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants | Node | Meaning |
|---|---|---|
| `T14_NUM1_READ` | `/Objects/T14/Num1,1:s=T14_Num1` | Node on B; B reads locally (`READ`) |
| `T14_NUM2_READ` | `/Objects/T14/Num2,1:s=T14_Num2` | Node on B; B reads locally (`READ`) |
| `T14_RESULT_WRITE` | `/Objects/T14/Result,1:s=T14_Result` | Node on B; B writes locally (`WRITE`) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A): empty, no connections.

2. **`SubApp_PC_B`** (device B), event connections:

    1. `NUM1.IND` → `TO_DINT_1.REQ`
    2. `NUM2.IND` → `TO_DINT_2.REQ`
    3. `TO_DINT_1.CNF` → `F_ADD.REQ`
    4. `TO_DINT_2.CNF` → `F_ADD.REQ`
    5. `F_ADD.CNF` → `TO_INT.REQ`
    6. `TO_INT.CNF` → `RESULT.REQ`
    7. `NUM1.INITO` → `NUM2.INIT`
    8. `NUM2.INITO` → `RESULT.INIT`

3. **`SubApp_PC_B`** (device B), data connections:

    1. `NUM1.RD_1` → `TO_DINT_1.IN`
    2. `NUM2.RD_1` → `TO_DINT_2.IN`
    3. `TO_DINT_1.OUT` → `F_ADD.IN1`
    4. `TO_DINT_2.OUT` → `F_ADD.IN2`
    5. `F_ADD.OUT` → `TO_INT.IN`
    6. `TO_INT.OUT` → `RESULT.SD_1`

## Technical Notes

- **Local access only**: all three nodes are addressed on B with `F_OPC_LOCAL` (`READ` and `WRITE` respectively); device A contains no logic in this training.
- **Type conversion**: the subscribed values are converted to `DINT` with `F_INT_TO_DINT`, added with `F_ADD`, and the result is converted back to `INT` with `F_DINT_TO_INT` before it is published.

## Note

- **Not tested on the device.** `Training_13_OPC_UA_RES` through `Training_18_OPC_UA_RES` have not yet been run on a device. This page describes only what is contained in `test_Training_14_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` and `myOpcUaAddresses.gcf`; whether the examples run as described has not been verified.
- Initialization comes from the resources: in `EMB_RES_A` and `EMB_RES_B`, `START.COLD` feeds `E_TRIG` (`EVENTTYPE = 'EInit'`), which triggers the initialization (`EInit`) of the application. That is why the first block of each chain has no connection of its own to its `INIT` input in the SubApp.

## Learning Objectives

- Building the adder example from `opcua.adoc` with `SUBSCRIBE` and `PUBLISH`.
- Converting `INT` values for the addition.

## Summary

`Training_14_OPC_UA_RES` is the adder from `opcua.adoc`: device B forms `Result` = `Num1` + `Num2` via `SUBSCRIBE`, `F_ADD` and `PUBLISH`; the SubApp of device A is empty.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
