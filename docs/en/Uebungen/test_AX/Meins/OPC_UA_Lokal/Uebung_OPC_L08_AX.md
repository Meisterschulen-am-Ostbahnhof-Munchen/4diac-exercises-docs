# Uebung_OPC_L08_AX: Toggle Flip-Flop Locally via Method Call

![Uebung_OPC_L08_AX_network](./Uebung_OPC_L08_AX_network.svg)

* * * * * * * * * *

## Introduction

`Uebung_OPC_L08_AX` is the local variant of `Training_08_OPC_UA_RES`: `SoftKey_F1` calls the toggle method via `localhost`, a toggle flip-flop drives `Output_Q1`, and the state comes back locally via `PUBLISH`/`SUBSCRIBE` to the SoftKey background. Both halves of the template run on **one** device.

The network consists of two SubApps without connections between them; coupling runs exclusively via the OPC-UA addresses.

Overview of all local exercises: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Function Blocks (FBs) Used

| Name | Type | Parameters | Explanation |
|---|---|---|---|
| `Softkey_Toggle_RPC_TO_Remote_BG_OPC` | [`Softkey_Toggle_RPC_TO_Remote_BG_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey_Toggle_RPC_TO_Remote_BG_OPC/) | `u16ObjId` = `SoftKey_F1`, `ID_TRIGGER_CALL` = `FF1_TRIGGER_CALL`, `ID_STATE_READ` = `FF1_STATE_READ` | Client side: `Softkey_IE` (event `SK_RELEASED`) triggers `CLIENT_0` (argument-less method call); `AX_SUBSCRIBE_1` reads the state and `GreenWhiteBackground1_AX` colours the SoftKey (TRUE = green, FALSE = white). |
| `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` | [`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Toggle_RPC_FROM_Remote_QXA_OPC_LOC/) | `Output` = `Output_Q1`, `ID_TRIGGER_METHOD` = `FF1_TRIGGER_METHOD`, `ID_STATE_WRITE` = `FF1_STATE_WRITE` | Server side (local variant `_LOC`): `SERVER_0` creates the method locally and acknowledges the call (`IND` → `RSP`); `AX_T_FF` toggles, `AX_SPLIT_2` distributes to `DigitalOutput_Q1` and to `AX_PUBLISH_1` (write state locally). |

## OPC-UA Address Space

The IDs come from `const\OpcUaLokal.gcf` (package `Meins::OPC_UA_Lokal::const`) and are computed with the functions of the `MyLib_OPC` lib (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Constant | Action | Node / method | Meaning |
|---|---|---|---|
| `FF1_TRIGGER_METHOD` | `CREATE_METHOD` | `/Objects/ToggleFF/FF1_Trigger` | Create toggle method locally |
| `FF1_TRIGGER_CALL` | `CALL_METHOD` | `/Objects/ToggleFF/FF1_Trigger` @ `opc.tcp://localhost:4840` | Call method via localhost |
| `FF1_STATE_WRITE` | `WRITE` | `/Objects/ToggleFF/FF1_State,1:s=FF1_State` | Write state locally (PUBLISH) |
| `FF1_STATE_READ` | `READ` | `/Objects/ToggleFF/FF1_State,1:s=FF1_State` | Read state locally (SUBSCRIBE) |

## Program Flow and Connections

1. **`Softkey_Toggle_RPC_TO_Remote_BG_OPC`**: no connections in the exercise network (the wiring is inside the composite).
2. **`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`**: no connections in the exercise network (the wiring is inside the composite).

## Technical Notes

- **Manual wiring**: in contrast to L09, SoftKey, client, flip-flop and split are visible as individual blocks inside the composites.
- **No network between client and server**: coupling runs only via the OPC-UA nodes; `FF1_TRIGGER_CALL` targets the own station via `LOCAL_OPC` (`opc.tcp://localhost:4840`).

## Learning Objectives

- Build a toggle flip-flop with a method call on one device.
- Combine local block variants (`_LOC`) and `CALL_METHOD` via `localhost`.
- Feed the state feedback locally via `PUBLISH`/`SUBSCRIBE` to the SoftKey background.

**Difficulty**: Advanced  
**Recommended sequence**: `Training_08_OPC_UA_RES` (template, two devices), `Uebung_OPC_L03_AX`

## Note / Result

- **Not tested on the device.** So far only L01, L03 and L19 have been tested on `FORTE_PC_AX`; `Uebung_OPC_L08_AX` has not yet been run on the device.
- Locally (without endpoint) `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` and `PUBLISH_0` work. `CALL_METHOD` and the client subscription, in contrast, run with an endpoint to the own station (`localhost` or own IP; both count as remote).
Note: Every `SERVER_0` needs `IND` → `RSP` directly on its own block, otherwise all OPC-UA publishes of the station stall for up to 4 s (rule see [overview](OPC_UA_Lokal.md)).

## Summary

`Uebung_OPC_L08_AX` runs the toggle pattern of `Training_08_OPC_UA_RES` on one device: method call via `localhost`, state locally via `PUBLISH`/`SUBSCRIBE`.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
