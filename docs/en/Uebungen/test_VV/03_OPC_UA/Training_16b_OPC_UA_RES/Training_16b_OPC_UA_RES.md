# Training_16b_OPC_UA_RES: Variables via CLIENT, Read After Acknowledgement — Two Devices

![Training_16b_OPC_UA_RES_network](./Training_16b_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_16b_OPC_UA_RES` implements the "objects/variables via CLIENT" example from `opcua.adoc` on two devices, but reads only after an acknowledgement from B: device A writes 42 to `Increment` on B, device B adds 1 and publishes `Incremented`. Device A subscribes to `Incremented` with [`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/) (with B's endpoint) and uses its `IND` to trigger the read via [`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/). The subscribed value itself is not evaluated; the subscription only serves as a trigger.

**B version of `Training_16_OPC_UA_RES`:** `READ_INCD.REQ` is no longer triggered by `WRITE_INC.CNF` (which only confirms the write to B) but by `SUBSCRIPTION.IND`, i.e. only once B has published `Incremented`. For this, device A additionally has a `SUBSCRIPTION` (`SUBSCRIBE_1`). The constants are named `T16B_*`, the nodes are under `/Objects/T16B/`. A subscription reports only changes: if `WRITE_INC` writes `INT#42` to `Increment` again, the value does not change and no new notification arrives. Presumably only the first key press therefore works (not checked on the device).


## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_16b_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `WRITE_INC` ([`CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_1_0/): `QI` = `TRUE`, `ID` = `T16B_INC_CLIENT_WRITE`, `SD_1` = `INT#42`), `READ_INCD` ([`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/): `QI` = `TRUE`, `ID` = `T16B_INCD_CLIENT_READ`), `RESULT_TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `SUBSCRIPTION` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T16B_INCD_SUBSCRIBE`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_16b_network.svg) | B (`192.168.1.12`) | `INCREMENT` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T16B_INC_READ`), `TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/): `IN2` = `DINT#1`), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)), `INCREMENTED` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T16B_INCD_WRITE`) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants | Node | Meaning |
|---|---|---|
| `T16B_INC_CLIENT_WRITE / T16B_INC_READ` | `/Objects/T16B/Increment,1:s=T16B_Increment` | Node on B; A writes via `CLIENT` (`WRITE` with B's endpoint), B reads locally (`READ`) |
| `T16B_INCD_WRITE / T16B_INCD_CLIENT_READ` | `/Objects/T16B/Incremented,1:s=T16B_Incremented` | Node on B; B writes locally (`WRITE`), A reads via `CLIENT` (`READ` with B's endpoint) |
| `T16B_INCD_SUBSCRIBE` | `/Objects/T16B/Incremented,1:s=T16B_Incremented` | same node as above; A subscribes to it with B's endpoint (`SUBSCRIBE`) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A), event connections:

    1. `TASTER_I1.IND` → `WRITE_INC.REQ`
    2. `SUBSCRIPTION.IND` → `READ_INCD.REQ`
    3. `TASTER_I1.INITO` → `WRITE_INC.INIT`
    4. `WRITE_INC.INITO` → `READ_INCD.INIT`
    5. `READ_INCD.CNF` → `RESULT_TO_DINT.REQ`

2. **`SubApp_PC_A`** (device A), data connections:

    1. `READ_INCD.RD_1` → `RESULT_TO_DINT.IN`

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

- **Read after acknowledgement**: `SUBSCRIPTION` (`SUBSCRIBE_1`) subscribes to `Incremented` on B (`T16B_INCD_SUBSCRIBE`, `F_OPC_REMOTE` with `SUBSCRIBE`); its `IND` starts `READ_INCD.REQ`.
- **Caveat**: `SUBSCRIPTION.IND` may also fire on the first subscription value, before B has incremented; the order is therefore better protected than in `Training_16_OPC_UA_RES`, but not formally guaranteed.
- **Type conversion**: as in `Training_16_OPC_UA_RES` (`F_INT_TO_DINT`, `F_ADD`, `F_DINT_TO_INT`).

## Note

- **Not tested on the device.** `Training_13_OPC_UA_RES` through `Training_18_OPC_UA_RES` have not yet been run on a device. This page describes only what is contained in `test_Training_16b_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` and `myOpcUaAddresses.gcf`; whether the examples run as described has not been verified.
- Initialization comes from the resources: in `EMB_RES_A` and `EMB_RES_B`, `START.COLD` feeds `E_TRIG` (`EVENTTYPE = 'EInit'`), which triggers the initialization (`EInit`) of the application. That is why the first block of each chain has no connection of its own to its `INIT` input in the SubApp.

## Learning Objectives

- Trigger the read operation only after an acknowledgement from the other device instead of directly after the write.
- Use `SUBSCRIBE_1` (with endpoint) as the trigger for a read via `CLIENT_0_1`.

**Recommended sequence**: `Training_16_OPC_UA_RES`.

## Summary

`Training_16b_OPC_UA_RES` is the B version of `Training_16_OPC_UA_RES`: device A reads `Incremented` only once device B has published it (trigger: subscription), not already after the write has been confirmed.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
