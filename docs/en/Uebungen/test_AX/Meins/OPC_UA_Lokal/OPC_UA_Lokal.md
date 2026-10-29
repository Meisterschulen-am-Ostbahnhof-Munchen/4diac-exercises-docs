# OPC_UA_Lokal: OPC-UA Examples on One Device

* * * * * * * * * *

## Introduction

The OPC-UA examples from `test_VV/sys/03_OPC_UA` (two devices) run here on **one** device (`test_AX`, `FORTE_PC_AX`). Each exercise is a SubApp. To try one out, select it in `test_AX.sys` via “Change Type” on the SubApp `Control`.

## What Works Locally (opcua.adoc)

| Action | FB | local |
|---|---|---|
| READ | SUBSCRIBE | yes |
| WRITE | PUBLISH | yes |
| CREATE_METHOD | SERVER | yes |
| CREATE/DELETE_OBJECT, CREATE/DELETE_VARIABLE | PUBLISH_0 | yes |
| CALL_METHOD | CLIENT | no |
| SUBSCRIBE (client subscription) | SUBSCRIBE | no |

“Local” means: without endpoint. What is not allowed locally runs as a remote call with an endpoint to the own station, here `opc.tcp://localhost:4840` (`LOCAL_OPC`). The own IP works the same way; both count as remote.

The IDs are in `const/OpcUaLokal.gcf`, built with [`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/) / [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/) and one `*_NODE` each.

## Exercises

| Exercise | Content |
|---|---|
| [`L01`](./Uebung_OPC_L01_AX.md) | like Training_01: `Input_I1` → PUBLISH, SUBSCRIBE → `Output_Q1` (tested on FORTE_PC_AX) |
| [`L02`](./Uebung_OPC_L02_AX.md) | like Training_02: protocol FBs used directly (`AX_PUBLISH_1` / `AX_SUBSCRIBE_1`) |
| [`L03`](./Uebung_OPC_L03_AX.md) | like Training_03 without VT: `Input_I1` calls the toggle method via localhost, Q1 flip-flop, Q2 state (tested on FORTE_PC_AX) |
| [`L04`](./Uebung_OPC_L04_AX.md)–[`L09`](./Uebung_OPC_L09_AX.md) | like Training_04–09 (SoftKeys, SR/Toggle, adapter variants), methods via localhost |
| [`L10`](./Uebung_OPC_L10_AX.md)–[`L12`](./Uebung_OPC_L12_AX.md) | like Training_10–12 (up/down with ILOCK, feedback, bundled) |
| [`L13`](./Uebung_OPC_L13_AX.md) | Flip-Flop (opcua.adoc): Q1 = NOT I1 via Flip/Flop nodes |
| [`L14`](./Uebung_OPC_L14_AX.md) | Adder (opcua.adoc): Num1 + Num2 = Result, values via UaExpert |
| [`L15`](./Uebung_OPC_L15_AX.md) | Method AddValues: I1 calls via CLIENT_2_1 with 43 and 10 |
| [`L16`](./Uebung_OPC_L16_AX.md) | Variables via CLIENT: I1 writes 42 to Increment, reads Incremented = 43 |
| [`L17`](./Uebung_OPC_L17_AX.md) | Client subscription: like L16, plus SUBSCRIBE with endpoint |
| [`L18`](./Uebung_OPC_L18_AX.md) | PUBLISH_0: I1/I2 create object/variable, I3/I4 delete them |
| [`L19`](./Uebung_OPC_L19_AX.md) | read twice locally: two SUBSCRIBE on one node (Q1 and Q2) (tested on FORTE_PC_AX) |

L19 needs a FORTE with eclipse-4diac/4diac-forte#1082. Without this change the second SUBSCRIBE reports “Cannot add another one” (see 4diac-documentation#123).

The documentation examples L13–L18 also exist on two devices as `test_VV/sys/03_OPC_UA/Training_13` to `_18`.

## Result

Tested on `FORTE_PC_AX` are L01, L03 (2026-10-18) and L19. All other exercises are not tested on the device.

## Rule: every SERVER needs IND → RSP

Every `SERVER_0` (in general every SERVER FB that creates a method) needs a connection `IND` → `RSP` directly on its own block. Without `RSP`, FORTE waits in `onServerMethodCall` for up to 4 seconds for the response and holds open62541's global service lock meanwhile. During that time **all** OPC-UA publishes of the station stall, not only the one method.

The actual reaction to the call hangs on a second, independent connection from the same `IND`. `RSP` is therefore never delayed. For methods with a return value (L15: `SERVER_1_2`), `RSP` is only triggered after the calculation. The calculation must therefore be finished immediately and must not wait for another event.

The rule is also described at [SERVER_0](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/net/SERVER_0/) in the library reference.

## Blocks

- Local variants (`_LOC`, PUBLISH instead of remote WRITE) in `MyLib_AX/typelib/sys`, e.g. [`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Toggle_RPC_FROM_Remote_QXA_OPC_LOC/), [`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/).
- Local net FBs in `MyLib_AX/typelib/net`: `*_SERVER_0_PUBLISH_1` (e.g. [`ASRT_AX_SERVER_0_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/ASRT_AX_SERVER_0_PUBLISH_1/)), [`A2X2_PUBLISH_2_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/A2X2_PUBLISH_2_SUBSCRIBE_2/), [`A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG/).
- Button instead of VT: [`Input_Toggle_RPC_TO_Remote_QXA_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_Toggle_RPC_TO_Remote_QXA_OPC/), [`Input_Toggle_RPC_TO_Remote_AX_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_Toggle_RPC_TO_Remote_AX_OPC/), [`Input_CLIENT_Method_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_CLIENT_Method_IE/).

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
