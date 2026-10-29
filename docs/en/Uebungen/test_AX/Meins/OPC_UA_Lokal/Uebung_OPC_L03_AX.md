# Uebung_OPC_L03_AX: Toggle Flip-Flop via Local OPC-UA Method (without VT)

![Uebung_OPC_L03_AX_network](./Uebung_OPC_L03_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L03_AX` corresponds to [`Training_03_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_03_OPC_UA_RES/Training_03_OPC_UA_RES.md) (test_VV), but local and **without VT**: instead of a SoftKey, a button press on `Input_I1` triggers the method call. `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` creates the toggle method locally (`SERVER_0`), clocks a toggle flip-flop and drives `Output_Q1`. The state returns locally via `PUBLISH`/`SUBSCRIBE` and is shown on `Output_Q2`. The interface of the SubApp is empty.

`LOCAL_OPC` is `"opc.tcp://localhost:4840"` (own server) and is only needed for `CALL_METHOD`: a `CALL_METHOD` is not allowed locally and therefore runs as a remote call with an endpoint to the own station (localhost or own IP; both count as remote).

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `Input_Toggle_RPC_TO_Remote_QXA_OPC` | [`Input_Toggle_RPC_TO_Remote_QXA_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_Toggle_RPC_TO_Remote_QXA_OPC/) | `Input` = `Input_I1`, `Output` = `Output_Q2`, `ID_TRIGGER_CALL` = `L03_FF1_TRIGGER_CALL`, `ID_STATE_READ` = `L03_FF1_STATE_READ` | [`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) (`BUTTON_PRESS_DOWN`) calls the method via [`CLIENT_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/CLIENT_0/); [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) reads the state locally, [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) shows it on `Output_Q2` |
| `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` | [`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Toggle_RPC_FROM_Remote_QXA_OPC_LOC/) | `Output` = `Output_Q1`, `ID_TRIGGER_METHOD` = `L03_FF1_TRIGGER_METHOD`, `ID_STATE_WRITE` = `L03_FF1_STATE_WRITE` | [`SERVER_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SERVER_0/) creates the method locally and clocks [`AX_T_FF`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_T_FF/); [`AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) fans the state out to [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) (`Output_Q1`) and [`AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) (local `PUBLISH`) |

## OPC-UA Address Space

All constants are in `const/OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`). The IDs are built with [`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/) (without endpoint) and [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/) (with endpoint) from one `*_NODE` each.

`LOCAL_OPC` is `"opc.tcp://localhost:4840"` (own server) and is only needed for `CALL_METHOD`: a `CALL_METHOD` is not allowed locally and therefore runs as a remote call with an endpoint to the own station (localhost or own IP; both count as remote).

| Constant | Value | Used by |
|---|---|---|
| `L03_FF1_TRIGGER_NODE` | `"/Objects/Lokal/L03/FF1_Trigger"` | Method node |
| `L03_FF1_TRIGGER_METHOD` | `MyLib_OPC::F_OPC_LOCAL("CREATE_METHOD", L03_FF1_TRIGGER_NODE)` | `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` (`ID_TRIGGER_METHOD`, `SERVER_0`) |
| `L03_FF1_TRIGGER_CALL` | `MyLib_OPC::F_OPC_REMOTE("CALL_METHOD", LOCAL_OPC, L03_FF1_TRIGGER_NODE)` | `Input_Toggle_RPC_TO_Remote_QXA_OPC` (`ID_TRIGGER_CALL`, `CLIENT_0`) |
| `L03_FF1_STATE_NODE` | `"/Objects/Lokal/L03/FF1_State,1:s=L03_FF1_State"` | Flip-flop state node |
| `L03_FF1_STATE_WRITE` | `MyLib_OPC::F_OPC_LOCAL("WRITE", L03_FF1_STATE_NODE)` | `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` (`ID_STATE_WRITE`) |
| `L03_FF1_STATE_READ` | `MyLib_OPC::F_OPC_LOCAL("READ", L03_FF1_STATE_NODE)` | `Input_Toggle_RPC_TO_Remote_QXA_OPC` (`ID_STATE_READ`) |

## Program Flow and Connections

1. **Two SubApps without a model connection**: the `.SUB` contains `Input_Toggle_RPC_TO_Remote_QXA_OPC` (`x=0`) and `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` (`x=4000`) and no connections; the coupling runs via OPC-UA.
2. **`Input_Toggle_RPC_TO_Remote_QXA_OPC`**: `TRIGGER_INPUT.IND` (button pressed) triggers `TRIGGER_CLIENT.REQ`; `CLIENT_0` calls `L03_FF1_TRIGGER_CALL` at localhost. In parallel, `STATE_SUBSCRIBE` (`AX_SUBSCRIBE_1`) delivers the state via adapter connection to `STATE_OUTPUT.OUT` (`Output_Q2`).
3. **`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`**: `TRIGGER_SERVER.IND` (method called) clocks `AX_T_FF.CLK` and is answered with `TRIGGER_SERVER.RSP`. `AX_T_FF.Q` goes to `AX_SPLIT_2.IN`; `OUT1` drives `DigitalOutput_Q1` (`Output_Q1`), `OUT2` feeds `STATE_PUBLISH.IN` (`AX_PUBLISH_1`, local `WRITE`).
4. **Runtime**: every button press on `Input_I1` toggles the flip-flop. `Output_Q1` follows the flip-flop directly, `Output_Q2` shows the state read back via OPC-UA.

## Result

Tested on FORTE_PC_AX (2026-10-18).
Note: Every `SERVER_0` needs `IND` → `RSP` directly on its own block, otherwise all OPC-UA publishes of the station stall for up to 4 s (rule see [overview](OPC_UA_Lokal.md)).

## Learning Objectives

- `CALL_METHOD` does not run locally but as a remote call with an endpoint to the own station (`LOCAL_OPC`).
- Creating a method locally (`CREATE_METHOD`/`SERVER_0`) and calling it (`CALL_METHOD`/`CLIENT_0`).
- State return path locally via `PUBLISH`/`SUBSCRIBE`.

**Recommended sequence**: `Uebung_OPC_L01_AX`, [`Training_03_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_03_OPC_UA_RES/Training_03_OPC_UA_RES.md)

## Summary

`Uebung_OPC_L03_AX` shows the toggle flip-flop pattern of Training_03 on one device and without VT: `Input_I1` calls the method via localhost, `Output_Q1` switches, `Output_Q2` shows the state.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
