# Uebung_134_AX: Übung zu ISOBUS Receive from Unclaimed Partner

This article describes the 4diac IDE sub-application Uebung_134_AX (Übung zu ISOBUS Receive from Unclaimed Partner).

----

![Uebung_134_AX_network](./Uebung_134_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu ISOBUS Receive from Unclaimed Partner**

-----

## Description and Components

The exercise consists of the sub-application Uebung_134_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **BaseMemberExternAdd**: Instance of type isobus::pgn::BaseMemberExternAdd.
  - Parameter u8CanIdx = NODE1
  - Parameter u8SA = 55
- **AlPgnRxNew8B**: Instance of type isobus::pgn::rx::AlPgnRxNew8B.
  - Parameter u32Pgn = PGN_PDU1_PropA
  - Parameter u16DaSize = 8
- **NmGetCfInfo**: Instance of type isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = thisMember
  - Parameter address = ADD_ALL_PASS
  - Parameter mask = FLT_ALL_PASS
- **NetEv2NetEv**: Instance of type isobus::pgn::NetEv2NetEv.
- **STRUCT_DEMUX**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.

### Connections and Interfaces

**Event Connections:**

- BaseMemberExternAdd.CNF -> NetEv2NetEv.REQ
- NetEv2NetEv.CNF -> AlPgnRxNew8B.install
- NmGetCfInfo.IND -> BaseMemberExternAdd.REQ
- AlPgnRxNew8B.IND -> STRUCT_DEMUX.REQ

**Data Connections:**

- BaseMemberExternAdd.s16Handle -> NetEv2NetEv.s16Handle
- NmGetCfInfo.sNetEv -> NetEv2NetEv.IN
- NetEv2NetEv. -> AlPgnRxNew8B.NmSource
- AlPgnRxNew8B.Data -> STRUCT_DEMUX.IN

-----

## Pattern with `NmGetCfInfo` (IND/RSP)

`NmGetCfInfo` reports a network event with `IND` and then waits for `RSP` (`bwaitingForRSP = TRUE`). Only after `RSP` does the block deliver the next event from its internal queue. This exercise is therefore built as follows:

- `IND` triggers `STRUCT_DEMUX` (`sNetEv`). Its `CNF` goes to `NetEv_Install.REQ`, `eNetEvent` to `NetEv_Install.eNetEvent`.
- The sub-application `NetEv_Install` (`MyLib::sys::NetEv_Install_Filter`) filters by event: for `Active` and `NewAddress` it outputs `INSTALL` (only the first time, afterwards `RSP`), all other events go directly to `RSP`. Event numbers from 7 on also end up at `RSP`.
- `installO` goes to `NetEv_Install.SET` and to `RSP`, `pgnERR` also goes to `RSP`.
- `INIT` stays unconnected (`EInit`), so the replay of all known events is triggered at start.
- All connections to `RSP` are hidden in the IDE.

State of the conversion: 2026-11-17, not yet tested on hardware. Open is whether the PGN still arrives after the partner logs off and on again, because it is installed only once per deploy.

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_134_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
