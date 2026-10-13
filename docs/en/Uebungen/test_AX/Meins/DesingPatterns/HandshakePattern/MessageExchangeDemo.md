# MessageExchangeDemo: Message Exchange via Handshake Adapters

![MessageExchangeDemo_network](./MessageExchangeDemo_network.svg)

* * * * * * * * * *

## Introduction

`MessageExchangeDemo` implements the "SoA implementation in function blocks" (IEC 61499 primer course, Module 6, Valeriy Vyatkin, slide 47) and reconstructs the CylH message exchange (slides 42-46) with **four cooperating function blocks**: `WorkpieceSensor` → `CylinderOrchestrator` → `CylinderService` / `DropSinkService`. They are coupled via three adapter connections of the `EVENT_HS_WSTRING` family.

### SubApp interface

| Name | Kind | Type | Description |
|---|---|---|---|
| `INIT` / `INITO` | Event | `EInit` | Initialization / confirmation |
| `DETECT` | Event input | `Event` | Simulated WPS sensor: a workpiece has arrived, starts the round trip |
| `AT_END` | Event input | `Event` | Simulated CYL.end sensor: pusher reached the end position |
| `AT_START` | Event input | `Event` | Simulated CYL.start sensor: pusher reached the start position again |
| `DONE` | Event output | `Event` | `WorkpieceSensor` received the `CNF`: round trip complete |
| `QI` / `QO` | Data | `BOOL` | Event qualifiers |
| `WPS_TripResult` | Data output | `WSTRING` | Payload of the last `CNF` at the `WorkpieceSensor` (expect `RSP_CYL_TRIP_COMPLETE`) |
| `WPS_TripCount` | Data output | `UINT` | Number of completed round trips |
| `DS_LastReqPayload` | Data output | `WSTRING` | Payload of the last `REQ` at the `DropSinkService` (expect `REQ_DROP`) |
| `DS_ReqCount` | Data output | `UINT` | Number of drop `REQ` received |

## Function Blocks (FBs) Used

All four blocks are located in the package `Meins::DesingPatterns::HandshakePattern`.

| Instance | Type | Explanation |
|---|---|---|
| `WorkpieceSensor` | `WorkpieceSensor` | On `DETECT`, starts the request `REQ_TRIP` via the adapter `ACTIVATE`; provides `TripResult`, `TripCount` and `DONE` |
| `Orchestrator` | `CylinderOrchestrator` | Receives the request from the sensor (`SRSP`), forwards it to the cylinder (`SREQ1`) and requests the drop at the sink (`SREQ2`) |
| `Cylinder` | `CylinderService` | Cylinder service with events `AT_END` and `AT_START`; answers via `SRSP` |
| `Sink` | `DropSinkService` | Sink service; provides `LastReqPayload` and `ReqCount` |

## Program Flow and Connections

- **Adapter coupling** (each with a comment in the source):
    - `WorkpieceSensor.ACTIVATE` → `Orchestrator.SRSP`: the sensor requests the round trip (`REQ_TRIP`); the orchestrator answers once the whole trip is confirmed complete.
    - `Orchestrator.SREQ1` → `Cylinder.SRSP`: the orchestrator forwards the trip request and reacts to the mid-trip `IND` (end position reached) and the closing `CNF` (trip complete).
    - `Orchestrator.SREQ2` → `Sink.SRSP`: the orchestrator requests the drop once the cylinder reports reaching the end position.
- **Initialization chain**: `INIT` → `WorkpieceSensor` → `Orchestrator` → `Cylinder` → `Sink` → `INITO`; correspondingly `QI` is passed through the four blocks to `QO`.
- **Events**: `DETECT` → `WorkpieceSensor.DETECT`, `AT_END` → `Cylinder.AT_END`, `AT_START` → `Cylinder.AT_START`, `WorkpieceSensor.DONE` → `DONE`.
- **Data**: `WorkpieceSensor.TripResult`/`TripCount` and `Sink.LastReqPayload`/`ReqCount` are exposed on the interface.

## Summary

`MessageExchangeDemo` shows the handshake idea as a message exchange between four services: sensor, orchestrator, cylinder and sink exchange requests and confirmations exclusively via adapters; the counters and payloads on the interface make the sequence observable.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
