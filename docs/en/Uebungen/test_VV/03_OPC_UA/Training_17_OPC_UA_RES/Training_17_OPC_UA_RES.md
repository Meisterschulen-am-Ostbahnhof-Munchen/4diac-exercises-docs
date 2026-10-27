# Training_17_OPC_UA_RES: Client Subscription over OPC-UA — Two Devices

![Training_17_OPC_UA_RES_network](./Training_17_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_17_OPC_UA_RES` implements the "client subscription" example from `opcua.adoc` on two devices. It corresponds to `Training_16_OPC_UA_RES` (device A writes 42 to `Increment` on B and reads `Incremented` via CLIENT; device B adds 1); device A additionally has a [`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/) with an endpoint on the node `Incremented` of device B.

The training is the two-device counterpart of `L17` in `test_AX/Meins/OPC_UA_Lokal`.

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_17_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `WRITE_INC` ([`CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_1_0/): `QI` = `TRUE`, `ID` = `T17_INC_CLIENT_WRITE`, `SD_1` = `INT#42`), `READ_INCD` ([`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/): `QI` = `TRUE`, `ID` = `T17_INCD_CLIENT_READ`), `RESULT_TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `SUBSCRIPTION` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T17_INCD_SUBSCRIBE`), `SUB_TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_17_network.svg) | B (`192.168.1.12`) | `INCREMENT` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T17_INC_READ`), `TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/): `IN2` = `DINT#1`), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)), `INCREMENTED` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T17_INCD_WRITE`) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants | Node | Meaning |
|---|---|---|
| `T17_INC_CLIENT_WRITE / T17_INC_READ` | `/Objects/T17/Increment,1:s=T17_Increment` | Node on B; A writes via `CLIENT` (`WRITE` with B's endpoint), B reads locally (`READ`) |
| `T17_INCD_WRITE / T17_INCD_CLIENT_READ` | `/Objects/T17/Incremented,1:s=T17_Incremented` | Node on B; B writes locally (`WRITE`), A reads via `CLIENT` (`READ` with B's endpoint) |
| `T17_INCD_SUBSCRIBE` | `/Objects/T17/Incremented,1:s=T17_Incremented` | same node as above; A subscribes to it with B's endpoint (`SUBSCRIBE`) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A), event connections:

    1. `TASTER_I1.IND` → `WRITE_INC.REQ`
    2. `WRITE_INC.CNF` → `READ_INCD.REQ`
    3. `TASTER_I1.INITO` → `WRITE_INC.INIT`
    4. `WRITE_INC.INITO` → `READ_INCD.INIT`
    5. `READ_INCD.CNF` → `RESULT_TO_DINT.REQ`
    6. `SUBSCRIPTION.IND` → `SUB_TO_DINT.REQ`

2. **`SubApp_PC_A`** (device A), data connections:

    1. `READ_INCD.RD_1` → `RESULT_TO_DINT.IN`
    2. `SUBSCRIPTION.RD_1` → `SUB_TO_DINT.IN`

3. **`SubApp_PC_B`** (device B), event connections:

    1. `INCREMENT.IND` → `TO_DINT.REQ`
    2. `TO_DINT.CNF` → `F_ADD.REQ`
    3. `F_ADD.CNF` → `TO_INT.REQ`
    4. `TO_INT.CNF` → `INCREMENTED.REQ`
    5. `INCREMENT.INITO` → `INCREMENTED.INIT`

4. **`SubApp_PC_B`** (device B), data connections:

    1. `INCREMENT.RD_1` → `TO_DINT.IN`
    2. `TO_DINT.OUT` → `F_ADD.IN1`
    3. `F_ADD.OUT` → `TO_INT.IN`
    4. `TO_INT.OUT` → `INCREMENTED.SD_1`

## Technical Notes

- **Two ways to the same value**: `READ_INCD` ([`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/)) reads `Incremented` once after the write, `SUBSCRIPTION` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/)) subscribes to the same node on B (`T17_INCD_SUBSCRIBE`, `F_OPC_REMOTE` with `SUBSCRIBE`) and reports every change with `IND`.
- **Difference to local access**: B reads `Increment` locally with `SUBSCRIBE_1` (`F_OPC_LOCAL`, `READ`), A subscribes to a node of B using an endpoint (`F_OPC_REMOTE`, `SUBSCRIBE`).
- **Type conversion**: as in `Training_16_OPC_UA_RES`; in addition `SUB_TO_DINT` converts the subscribed value to `DINT`.

## Note

- **Not tested on the device.** `Training_13_OPC_UA_RES` through `Training_18_OPC_UA_RES` have not yet been run on a device. This page describes only what is contained in `test_Training_17_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` and `myOpcUaAddresses.gcf`; whether the examples run as described has not been verified.
- Initialization comes from the resources: in `EMB_RES_A` and `EMB_RES_B`, `START.COLD` feeds `E_TRIG` (`EVENTTYPE = 'EInit'`), which triggers the initialization (`EInit`) of the application. That is why the first block of each chain has no connection of its own to its `INIT` input in the SubApp.

## Learning Objectives

- Subscribing to a node of another device with `SUBSCRIBE_1` (with endpoint) in addition to reading it via `CLIENT_0_1`.
- Comparing read-on-request and subscription of the same node.

**Recommended sequence**: `Training_16_OPC_UA_RES`.

## Summary

`Training_17_OPC_UA_RES` extends `Training_16_OPC_UA_RES` with a client subscription: device A subscribes to `Incremented` of device B in addition to reading it via CLIENT.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
