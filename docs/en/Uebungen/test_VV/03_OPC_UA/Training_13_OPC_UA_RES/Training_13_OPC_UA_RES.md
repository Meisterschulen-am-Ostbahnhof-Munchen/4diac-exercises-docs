# Training_13_OPC_UA_RES: Flip-Flop over OPC-UA — Two Devices

![Training_13_OPC_UA_RES_network](./Training_13_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Introduction

`Training_13_OPC_UA_RES` implements the flip-flop example from `opcua.adoc` on two devices: device A writes the state of the physical input `Input_I1` via OPC-UA to the node `Flip` on device B and shows the node `Flop` of device B on the physical output `Output_Q1`. Device B subscribes to `Flip` locally, routes the value via [`E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SWITCH/) to the set or reset input of [`E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SR/) and publishes the state of `E_SR` locally to `Flop`. According to the SubApp comment, `Output_Q1` therefore shows `Flop` (= NOT `Input_I1`).

The training is the two-device counterpart of `L13` in `test_AX/Meins/OPC_UA_Lokal` (where the same example runs on a single device).

## System Structure

Device A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, station 11, `192.168.1.11`) with `EMB_RES_A`, device B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, station 12, `192.168.1.12`) with `EMB_RES_B`, connected via the `Ethernet` segment. Each resource contains only `E_TRIG` (`EVENTTYPE` = `'EInit'`) triggered by `START.COLD`; all logic lives in the SubApps.

## SubApps Used

| SubApp | Device | Content |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_13_network.svg) | A (`192.168.1.11`) | Two composites that contain the OPC-UA protocol themselves: `INPUT_I1` ([`logiBUS_IXA_TO_CLIENT_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_CLIENT_OPC/): `Input` = `Input_I1`, `ID` = `T13_FLIP_REMOTE_WRITE`) writes the input to `Flip` on B; `OUTPUT_Q1` ([`logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/): `Output` = `Output_Q1`, `ID` = `T13_FLOP_REMOTE_SUBSCRIBE`) subscribes to `Flop` on B and thereby drives the output |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_13_network.svg) | B (`192.168.1.12`) | `FLIP` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T13_FLIP_READ`), `E_SWITCH` ([`E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SWITCH/)), `E_SR` ([`E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SR/)), `FLOP` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T13_FLOP_WRITE`) |

## OPC-UA Address Space

The IDs come from `myOpcUaAddresses.gcf`; the stations `PC_A`/`PC_B` are defined only there, the OPC-UA IDs are computed with the functions of the `MyLib_OPC` library. Structure and computation: [OPC-UA addresses](../OPC_UA_Adressen.md).

| Constants | Node | Meaning |
|---|---|---|
| `T13_FLIP_REMOTE_WRITE / T13_FLIP_READ` | `/Objects/T13/Flip,1:s=T13_Flip` | Node on B; A writes (`WRITE` with B's endpoint), B reads locally (`READ`) |
| `T13_FLOP_WRITE / T13_FLOP_REMOTE_SUBSCRIBE` | `/Objects/T13/Flop,1:s=T13_Flop` | Node on B; B writes locally (`WRITE`), A subscribes (`SUBSCRIBE` with B's endpoint) |

## Program Flow and Connections

1. **`SubApp_PC_A`** (device A): no connections in the SubApp; `INPUT_I1` and `OUTPUT_Q1` are independent and are coupled only via OPC-UA.

2. **`SubApp_PC_B`** (device B), event connections:

    1. `FLIP.IND` → `E_SWITCH.EI`
    2. `E_SWITCH.EO0` → `E_SR.S`
    3. `E_SWITCH.EO1` → `E_SR.R`
    4. `E_SR.EO` → `FLOP.REQ`
    5. `FLIP.INITO` → `FLOP.INIT`

3. **`SubApp_PC_B`** (device B), data connections:

    1. `FLIP.RD_1` → `E_SWITCH.G`
    2. `E_SR.Q` → `FLOP.SD_1`

## Technical Notes

- **Local and remote**: device B accesses its own nodes locally (`F_OPC_LOCAL`: `READ` for `FLIP`, `WRITE` for `FLOP`), device A accesses them with B's endpoint (`PC_B_OPC`) (`F_OPC_REMOTE`: `WRITE` and `SUBSCRIBE` respectively).
- **Flip-flop**: depending on the value of `FLIP` (`G`), `E_SWITCH` fires `EO0` (to `E_SR.S`) or `EO1` (to `E_SR.R`); `E_SR.Q` is published to `Flop`.

## Note

- **Not tested on the device.** `Training_13_OPC_UA_RES` through `Training_18_OPC_UA_RES` have not yet been run on a device. This page describes only what is contained in `test_Training_13_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` and `myOpcUaAddresses.gcf`; whether the examples run as described has not been verified.
- Initialization comes from the resources: in `EMB_RES_A` and `EMB_RES_B`, `START.COLD` feeds `E_TRIG` (`EVENTTYPE = 'EInit'`), which triggers the initialization (`EInit`) of the application. That is why the first block of each chain has no connection of its own to its `INIT` input in the SubApp.

## Learning Objectives

- Building the flip-flop example from `opcua.adoc` on two devices.
- Serving nodes locally via `PUBLISH`/`SUBSCRIBE` and writing or subscribing to them from another device using an endpoint.

## Summary

`Training_13_OPC_UA_RES` is the flip-flop from `opcua.adoc` on two devices: A writes `Input_I1` to `Flip` and shows `Flop` on `Output_Q1`; B derives `Flop` from `Flip` using `E_SWITCH` and `E_SR`.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
