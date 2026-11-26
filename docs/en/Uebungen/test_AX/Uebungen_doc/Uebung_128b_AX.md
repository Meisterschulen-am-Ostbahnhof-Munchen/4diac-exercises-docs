# Uebung_128b_AX: Übung zu ISOBUS Send Message GLOBAL TP BAM

This article describes the 4diac IDE sub-application Uebung_128b_AX (Übung zu ISOBUS Send Message GLOBAL TP BAM).

----

![Uebung_128b_AX_network](./Uebung_128b_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu ISOBUS Send Message GLOBAL TP BAM**

-----

## Counterpart

There is no counterpart yet: the existing receive blocks only pass 8 bytes on to the application, a receive block for longer PGNs (`AlPgnRxNew_TP`) is in progress. See also the [CAN overview](Uebungen_CAN_Uebersicht.md).

## Description and Components

The exercise consists of the sub-application Uebung_128b_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **STRUCT_DEMUX_3**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo_1**: Instance of type isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = thisMember
  - Parameter address = ADD_ALL_PASS
  - Parameter mask = FLT_ALL_PASS
- **STRUCT_DEMUX_4**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_5**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **AlPgnTxNew_TP**: Instance of type isobus::pgn::tx::AlPgnTxNew_TP.
  - Parameter u32Pgn = PGN_PDU1_PropA
  - Input u16DaSize: comes from `F_LEN_ARRAY.OUT` (length of `INIT_ARR_0032_BYTE.D1`, 32 bytes), no fixed value
  - Parameter u8Priority = 3
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **NetEv2NetEv**: Instance of type isobus::pgn::NetEv2NetEv.
  - Parameter s16Handle = GLOBAL_A
- **INIT_ARR_0032_BYTE**: Instance of type eclipse4diac::convert::providers::PROVIDE_ARR_0032_BYTE.
  - Parameter D1 = [16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00, 16#00]

### Connections and Interfaces

**Event Connections:**

- NmGetCfInfo_1.IND -> STRUCT_DEMUX_3.REQ
- NmGetCfInfo_1.IND -> STRUCT_DEMUX_4.REQ
- NmGetCfInfo_1.IND -> STRUCT_DEMUX_5.REQ
- NmGetCfInfo_1.IND -> NetEv2NetEv.REQ
- DigitalInput_CLK_I1.IND -> AlPgnTxNew_TP.REQ
- NetEv2NetEv.CNF -> AlPgnTxNew_TP.install
- INIT_ARR_0032_BYTE.INITO -> AlPgnTxNew_TP.INIT

**Data Connections:**

- NmGetCfInfo_1.sNameField -> STRUCT_DEMUX_3.IN
- NmGetCfInfo_1.sNetEv -> STRUCT_DEMUX_5.IN
- NmGetCfInfo_1.sCfInfo -> STRUCT_DEMUX_4.IN
- NmGetCfInfo_1.sNetEv -> NetEv2NetEv.IN
- NetEv2NetEv. -> AlPgnTxNew_TP.NmDestin
- INIT_ARR_0032_BYTE.D1 -> AlPgnTxNew_TP.Data

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

Exercise Uebung_128b_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
