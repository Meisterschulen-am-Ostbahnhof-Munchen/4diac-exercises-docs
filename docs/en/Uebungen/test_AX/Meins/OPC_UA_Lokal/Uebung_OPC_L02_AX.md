# Uebung_OPC_L02_AX: Local I1→Q1 with Protocol FBs Used Directly

![Uebung_OPC_L02_AX_network](./Uebung_OPC_L02_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L02_AX` corresponds to [`Training_02_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_02_OPC_UA_RES/Training_02_OPC_UA_RES.md) (test_VV), but local: the protocol blocks [`AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) and [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) sit directly in the network instead of in a composite. `logiBUS_IXA` → `AX_PUBLISH_1` writes the input locally to the node, `AX_SUBSCRIBE_1` → `logiBUS_QXA` reads it locally and drives the output. The interface of the SubApp is empty.

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `INPUT_I1` | [`logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `QI` = `TRUE`, `Input` = `Input_I1` | Reads `Input_I1` and provides the state as an AX adapter plug |
| `PUBLISH_Q1` | [`AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) | `QI` = `TRUE`, `ID` = `Q1_WRITE` | Writes the state locally to the OPC-UA node (`WRITE` without endpoint) |
| `SUBSCRIBE_Q1` | [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `Q1_READ` | Reads the same node locally (`READ` without endpoint) |
| `OUTPUT_Q1` | [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q1` | Drives `Output_Q1`, receives the state via the AX adapter socket |

## OPC-UA Address Space

All constants are in `const/OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`). The IDs are built with [`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/) (without endpoint) and [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/) (with endpoint) from one `*_NODE` each.

| Constant | Value | Used by |
|---|---|---|
| `Q1_NODE` | `"/Objects/DigitalOutput/Q1,1:s=Output_Q1"` | Node (as in test_VV) |
| `Q1_WRITE` | `MyLib_OPC::F_OPC_LOCAL("WRITE", Q1_NODE)` | `PUBLISH_Q1` (`ID`) |
| `Q1_READ` | `MyLib_OPC::F_OPC_LOCAL("READ", Q1_NODE)` | `SUBSCRIBE_Q1` (`ID`) |

## Program Flow and Connections

1. `INPUT_I1.INITO` → `PUBLISH_Q1.INIT` (event connection).
2. `SUBSCRIBE_Q1.INITO` → `OUTPUT_Q1.INIT` (event connection).
3. `INPUT_I1.IN` → `PUBLISH_Q1.IN` (adapter connection).
4. `SUBSCRIBE_Q1.OUT` → `OUTPUT_Q1.OUT` (adapter connection).
5. There is no model connection between `PUBLISH_Q1` and `SUBSCRIBE_Q1`; they are coupled only through the OPC-UA node `Q1_NODE`.

## Result

Not tested on the device.

## Learning Objectives

- Wiring the protocol blocks `AX_PUBLISH_1`/`AX_SUBSCRIBE_1` directly instead of encapsulating them in a composite.
- Assigning the actions: local `WRITE` (PUBLISH) and local `READ` (SUBSCRIBE).
- Initialization order via `INITO` → `INIT`.

**Recommended sequence**: `Uebung_OPC_L01_AX`, [`Training_02_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_02_OPC_UA_RES/Training_02_OPC_UA_RES.md)

## Summary

`Uebung_OPC_L02_AX` shows the same I1→Q1 pattern as L01, but with the protocol blocks `AX_PUBLISH_1` and `AX_SUBSCRIBE_1` wired directly in the network.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
