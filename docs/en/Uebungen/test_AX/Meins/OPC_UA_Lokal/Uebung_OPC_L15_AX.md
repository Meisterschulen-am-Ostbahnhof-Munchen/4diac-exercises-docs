# Uebung_OPC_L15_AX: Local method AddValues: SERVER_1_2 and CLIENT_2_1

![Uebung_OPC_L15_AX_network](./Uebung_OPC_L15_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L15_AX` creates the method AddValues from `opcua.adoc` locally (`SERVER_1_2` + `F_ADD`) and calls it with `CLIENT_2_1` using the values 43 and 10 (result 53) via the button `Input_I1`. Creating the method is local (`CREATE_METHOD` without endpoint); the call (`CALL_METHOD`) needs an endpoint and therefore goes to the station itself (`LOCAL_OPC`, localhost; counts as remote).

Series overview: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

Comment in the `.SUB` (German original): "Methode AddValues (opcua.adoc): SERVER_1_2 + F_ADD; Input_I1 ruft sie per CLIENT_2_1 mit 43 und 10 auf (Ergebnis 53) - lokal auf einem Geraet"

## Function Blocks (FBs) Used

### `TASTER_I1`

- Type: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
- Parameters:

    - `QI` = `TRUE`
    - `Input` = `Input_I1`
    - `InputEvent` = `BUTTON_PRESS_DOWN`

- Explanation: Button `Input_I1` (event `BUTTON_PRESS_DOWN`, i.e. on press). Triggers the method call.

### `ADD_CALL`

- Type: `iec61499::net::CLIENT_2_1`
- Parameters:

    - `QI` = `TRUE`
    - `ID` = `L15_ADD_CALL`
    - `SD_1` = `INT#43`
    - `SD_2` = `INT#10`

- Explanation: Calls the method AddValues via an endpoint (`LOCAL_OPC` = `opc.tcp://localhost:4840`, the station itself) with `SD_1` = `INT#43` and `SD_2` = `INT#10`. The result (53) arrives at `RD_1`.

### `RESULT_TO_DINT`

- Type: `iec61131::conversion::F_INT_TO_DINT`
- Parameters: none set

- Explanation: Converts the call result from INT to DINT.

### `ADD_VALUES`

- Type: `iec61499::net::SERVER_1_2`
- Parameters:

    - `QI` = `TRUE`
    - `ID` = `L15_ADD_METHOD`

- Explanation: Creates the method AddValues locally (no endpoint, `CREATE_METHOD`) with two input arguments (`RD_1`, `RD_2`) and one output argument (`SD_1`). `IND` signals a call, `RSP` sends the response.

### `TO_DINT_1`

- Type: `iec61131::conversion::F_INT_TO_DINT`
- Parameters: none set

- Explanation: Converts the first argument (`RD_1`) to DINT.

### `TO_DINT_2`

- Type: `iec61131::conversion::F_INT_TO_DINT`
- Parameters: none set

- Explanation: Converts the second argument (`RD_2`) to DINT.

### `F_ADD`

- Type: `iec61131::arithmetic::F_ADD`
- Parameters: none set

- Explanation: Adds both arguments (`IN1` + `IN2`).

### `TO_INT`

- Type: `iec61131::conversion::F_DINT_TO_INT`
- Parameters: none set

- Explanation: Converts the sum back to INT for `SD_1` of the method.

## OPC-UA Addresses (`OpcUaLokal.gcf`)

The IDs come from `const/OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`).

| Constant | Action | Node / ID |
|---|---|---|
| `L15_ADD_METHOD` | CREATE_METHOD | `/Objects/Lokal/L15/AddValues` |
| `L15_ADD_CALL` | CALL_METHOD (endpoint `LOCAL_OPC`) | `/Objects/Lokal/L15/AddValues` |

## Program Flow and Connections

### Event connections

1. `TASTER_I1.IND` → `ADD_CALL.REQ`
2. `TASTER_I1.INITO` → `ADD_CALL.INIT`
3. `ADD_CALL.CNF` → `RESULT_TO_DINT.REQ`
4. `ADD_VALUES.IND` → `TO_DINT_1.REQ`
5. `TO_DINT_1.CNF` → `TO_DINT_2.REQ`
6. `TO_DINT_2.CNF` → `F_ADD.REQ`
7. `F_ADD.CNF` → `TO_INT.REQ`
8. `TO_INT.CNF` → `ADD_VALUES.RSP`

### Data connections

1. `ADD_CALL.RD_1` → `RESULT_TO_DINT.IN`
2. `ADD_VALUES.RD_1` → `TO_DINT_1.IN`
3. `ADD_VALUES.RD_2` → `TO_DINT_2.IN`
4. `TO_DINT_1.OUT` → `F_ADD.IN1`
5. `TO_DINT_2.OUT` → `F_ADD.IN2`
6. `F_ADD.OUT` → `TO_INT.IN`
7. `TO_INT.OUT` → `ADD_VALUES.SD_1`

## Note / Result

Not tested on the device. Expected: the call with 43 and 10 returns 53.

The same example on two devices: [Training_15_OPC_UA_RES](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/en/latest/Uebungen/test_VV/03_OPC_UA/Training_15_OPC_UA_RES/Training_15_OPC_UA_RES/).
Note: Every `SERVER_0` needs `IND` → `RSP` directly on its own block, otherwise all OPC-UA publishes of the station stall for up to 4 s (rule see [overview](OPC_UA_Lokal.md)). For methods with a return value (`SERVER_1_2`), `RSP` comes only after the immediate calculation.

## Learning Objectives

- Create a method locally with `SERVER_1_2` and answer it with `RSP`
- Call a method with `CLIENT_2_1` (endpoint required, here the station itself)
- Difference between local (no endpoint) and remote (with endpoint) for `CREATE_METHOD` and `CALL_METHOD`

## Summary

The exercise shows method creation and method call within the same device: the server part local, the client part through the endpoint of the station itself.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
