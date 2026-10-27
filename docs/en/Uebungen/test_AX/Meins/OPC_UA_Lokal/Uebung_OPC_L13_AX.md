# Uebung_OPC_L13_AX: Flip-Flop (opcua.adoc) Locally on One Device

![Uebung_OPC_L13_AX_network](./Uebung_OPC_L13_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L13_AX` implements the flip-flop example from `opcua.adoc` locally on one device: `Input_I1` writes the OPC-UA node `Flip`, a `SUBSCRIBE` reads it, `E_SWITCH` and `E_SR` form the flip-flop and a `PUBLISH` writes the node `Flop`, which `Output_Q1` reads via `SUBSCRIBE`. Thus **Q1 = NOT I1** holds.

Only local actions (`WRITE`/`READ` without endpoint) are used.

Overview of all local exercises: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Function Blocks (FBs) Used

| Name | Type | Parameters | Explanation |
|---|---|---|---|
| `INPUT_I1` | [`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/) | `Input` = `Input_I1`, `ID` = `L13_FLIP_WRITE` | Reads `Input_I1` and writes the value locally via `PUBLISH` to the node `Flip`. |
| `OUTPUT_Q1` | [`logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/) | `Output` = `Output_Q1`, `ID` = `L13_FLOP_READ` | Reads the node `Flop` locally via `SUBSCRIBE` and drives `Output_Q1`. |
| `FLIP` | [`iec61499::net::SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `L13_FLIP_READ` | Reads `Flip` locally; `IND` signals each update, `RD_1` delivers the value. |
| `E_SWITCH` | [`iec61499::events::E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SWITCH/) | - | Forwards the event depending on the value at `G` to `EO0` (FALSE) or `EO1` (TRUE). |
| `E_SR` | [`iec61499::events::E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SR/) | - | Set/reset latch: `S` sets `Q`, `R` clears `Q`; `EO` signals each change. |
| `FLOP` | [`iec61499::net::PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/) | `QI` = `TRUE`, `ID` = `L13_FLOP_WRITE` | Writes `Q` of the flip-flop locally to the node `Flop`. |

## OPC-UA Address Space

The IDs come from `const\OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`) and are computed with the functions of the `MyLib_OPC` lib (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Constant | Action | Node / method | Meaning |
|---|---|---|---|
| `L13_FLIP_WRITE` | `WRITE` | `/Objects/Lokal/L13/Flip,1:s=L13_Flip` | Input_I1 writes Flip locally (PUBLISH) |
| `L13_FLIP_READ` | `READ` | `/Objects/Lokal/L13/Flip,1:s=L13_Flip` | Read Flip locally (SUBSCRIBE) |
| `L13_FLOP_WRITE` | `WRITE` | `/Objects/Lokal/L13/Flop,1:s=L13_Flop` | Write Flop locally (PUBLISH) |
| `L13_FLOP_READ` | `READ` | `/Objects/Lokal/L13/Flop,1:s=L13_Flop` | Output_Q1 reads Flop locally (SUBSCRIBE) |

## Program Flow and Connections

The SubApps `INPUT_I1` and `OUTPUT_Q1` are not wired; they are connected to the rest only via the OPC-UA nodes `Flip` and `Flop`.

Event connections:

1. `FLIP.IND` → `E_SWITCH.EI`
2. `E_SWITCH.EO0` → `E_SR.S`
3. `E_SWITCH.EO1` → `E_SR.R`
4. `E_SR.EO` → `FLOP.REQ`
5. `FLIP.INITO` → `FLOP.INIT`

Data connections:

1. `FLIP.RD_1` → `E_SWITCH.G`
2. `E_SR.Q` → `FLOP.SD_1`

## Technical Notes

- **Inversion by wiring**: `FLIP.RD_1` drives `E_SWITCH.G`; on TRUE `EO1` goes to `E_SR.R` (Flop = FALSE), on FALSE `EO0` goes to `E_SR.S` (Flop = TRUE). Hence Flop = NOT Flip.
- **Standard blocks instead of adapters**: `FLIP`/`FLOP` are the standard blocks `SUBSCRIBE_1`/`PUBLISH_1` with event and data connections.
- **Initialisation**: `FLIP.INITO` triggers `FLOP.INIT`.

## Learning Objectives

- Build the flip-flop example from `opcua.adoc` locally with `SUBSCRIBE_1`, `E_SWITCH`, `E_SR` and `PUBLISH_1`.
- Achieve an inversion (Q1 = NOT I1) purely through event wiring.
- Decouple input and output via two local OPC-UA nodes.

**Difficulty**: Advanced  
**Recommended sequence**: `Uebung_OPC_L01_AX`

## Note / Result

- **Not tested on the device.** So far only L01, L03 and L19 have been tested on `FORTE_PC_AX`; `Uebung_OPC_L13_AX` has not yet been run on the device.
- Locally (without endpoint) `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` and `PUBLISH_0` work. `CALL_METHOD` and the client subscription, in contrast, run with an endpoint to the own station (`localhost` or own IP; both count as remote).

## Summary

`Uebung_OPC_L13_AX` shows the flip-flop example from `opcua.adoc` locally: `Input_I1` → node `Flip` → `E_SWITCH`/`E_SR` → node `Flop` → `Output_Q1`, i.e. Q1 = NOT I1.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
