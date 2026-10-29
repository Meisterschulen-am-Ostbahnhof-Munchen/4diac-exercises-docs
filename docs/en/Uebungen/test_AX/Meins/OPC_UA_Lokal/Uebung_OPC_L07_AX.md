# Uebung_OPC_L07_AX: Set/Reset Flip-Flop Locally via Method Call (Adapter Variant)

![Uebung_OPC_L07_AX_network](./Uebung_OPC_L07_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L07_AX` is the local variant of `Training_07_OPC_UA_RES` (adapter variant of Training_05): two SoftKeys set (`SoftKey_F1`) and reset (`SoftKey_F2`) a Set/Reset state which drives `Output_Q1`. The two halves of the template (device A and device B) run here on **one** device: the method calls go to the own station via `localhost`, the state is written locally via `PUBLISH` and read locally via `SUBSCRIBE` to colour the SoftKey background.

The network consists of two SubApps without connections between them; coupling runs exclusively via the OPC-UA addresses (see below).

Overview of all local exercises: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Function Blocks (FBs) Used

| Name | Type | Parameters | Explanation |
|---|---|---|---|
| `SoftKeySR_PC_A_OPC_Adapter` | [`SoftKeySR_PC_A_OPC_Adapter`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeySR_PC_A_OPC_Adapter/) | `u16ObjId_SET` = `SoftKey_F1`, `u16ObjId_RESET` = `SoftKey_F2`, `ID_SET_CALL` = `SR2_SET_CALL`, `ID_RESET_CALL` = `SR2_RESET_CALL`, `ID_STATE_READ` = `SR2_STATE_READ` | Client side (HMI): `SoftKeySR_ASR_AX` bundles the two SoftKeys and the `GreenWhiteBackground` behind an `ASR_AX` plug; `ASR_AX_CLIENT_0_SUBSCRIBE_1` (`TRIGGER`) calls the Set/Reset method via `CALL_METHOD` and reads the state via `SUBSCRIBE`. |
| `SoftKeySR_PC_B_OPC_Adapter` | [`SoftKeySR_PC_B_OPC_Adapter_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeySR_PC_B_OPC_Adapter_LOC/) | `Output` = `Output_Q1`, `ID_SET_METHOD` = `SR2_SET_METHOD`, `ID_RESET_METHOD` = `SR2_RESET_METHOD`, `ID_STATE_WRITE` = `SR2_STATE_WRITE` | Server side (local variant `_LOC`): `ASR_AX_SERVER_0_PUBLISH_1` creates the two methods locally (`CREATE_METHOD`) and writes the state locally via `PUBLISH`; `ASR_AX_AX_SPLIT` distributes to `ASR_AX_SR_2` (`FLIPFLOP`) and to `DigitalOutput_Q1`. |

## OPC-UA Address Space

The IDs come from `const\OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`) and are computed with the functions of the `MyLib_OPC` lib (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Constant | Action | Node / method | Meaning |
|---|---|---|---|
| `SR2_SET_METHOD` | `CREATE_METHOD` | `/Objects/SRFlipflop/SR2_Set` | Create Set method locally |
| `SR2_SET_CALL` | `CALL_METHOD` | `/Objects/SRFlipflop/SR2_Set` @ `opc.tcp://localhost:4840` | Call Set method via localhost |
| `SR2_RESET_METHOD` | `CREATE_METHOD` | `/Objects/SRFlipflop/SR2_Reset` | Create Reset method locally |
| `SR2_RESET_CALL` | `CALL_METHOD` | `/Objects/SRFlipflop/SR2_Reset` @ `opc.tcp://localhost:4840` | Call Reset method via localhost |
| `SR2_STATE_WRITE` | `WRITE` | `/Objects/SRFlipflop/SR2_State,1:s=SR2_State` | Write state locally (PUBLISH) |
| `SR2_STATE_READ` | `READ` | `/Objects/SRFlipflop/SR2_State,1:s=SR2_State` | Read state locally (SUBSCRIBE) |

## Program Flow and Connections

1. **`SoftKeySR_PC_A_OPC_Adapter`**: no connections in the exercise network (the wiring is inside the composite).
2. **`SoftKeySR_PC_B_OPC_Adapter`**: no connections in the exercise network (the wiring is inside the composite).

## Technical Notes

- **No network between A and B**: the link between client and server half runs only via the OPC-UA nodes; `SR2_SET_CALL`/`SR2_RESET_CALL` target the own station via `LOCAL_OPC` (`opc.tcp://localhost:4840`).
- **Same state node, two roles**: `SR2_STATE_WRITE` (B, `PUBLISH`) and `SR2_STATE_READ` (A, `SUBSCRIBE`) point to the same node `SR2_State`.

## Learning Objectives

- Merge the two halves of a client/server exercise on one device.
- Use local block variants (`_LOC`) next to the client block with endpoint.
- Distinguish the method call via `localhost` from local `PUBLISH`/`SUBSCRIBE` for the state.

**Difficulty**: Advanced  
**Recommended sequence**: `Training_07_OPC_UA_RES` (template, two devices)

## Note / Result

- **Not tested on the device.** So far only L01, L03 and L19 have been tested on `FORTE_PC_AX`; `Uebung_OPC_L07_AX` has not yet been run on the device.
- Locally (without endpoint) `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` and `PUBLISH_0` work. `CALL_METHOD` and the client subscription, in contrast, run with an endpoint to the own station (`localhost` or own IP; both count as remote).
Note: Every `SERVER_0` needs `IND` → `RSP` directly on its own block, otherwise all OPC-UA publishes of the station stall for up to 4 s (rule see [overview](OPC_UA_Lokal.md)). The SubApp `MyLib::net::ASR_AX_SERVER_0_PUBLISH_1` contains the self-loop.

## Summary

`Uebung_OPC_L07_AX` merges the two devices of `Training_07_OPC_UA_RES` onto one device: Set/Reset via method call over `localhost`, state locally via `PUBLISH`/`SUBSCRIBE`.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
