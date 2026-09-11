# Uebung_135_AX: Übung zu ISOBUS Receive Message

This article describes the 4diac IDE sub-application Uebung_135_AX (Übung zu ISOBUS Receive Message).

----

![Uebung_135_AX_network](./Uebung_135_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu ISOBUS Receive Message**

-----

## Description and Components

The exercise consists of the sub-application Uebung_135_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **STRUCT_DEMUX_3**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo_1**: Instance of type isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = network
  - Parameter address = PRIM_TECU_ADD
  - Parameter mask = PRIM_TECU_FLT
- **STRUCT_DEMUX_4**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_5**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **AlPgnRxNew8B**: Instance of type isobus::pgn::rx::AlPgnRxNew8B.
  - Parameter u32Pgn = PGN_ELECTRONIC_STEERING_CONTROL
  - Parameter u16DaSize = 8
  - Parameter u8Priority = 3
- **STRUCT_DEMUX**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.

### Connections and Interfaces

**Event Connections:**

- NmGetCfInfo_1.IND -> STRUCT_DEMUX_3.REQ
- NmGetCfInfo_1.IND -> STRUCT_DEMUX_4.REQ
- NmGetCfInfo_1.IND -> STRUCT_DEMUX_5.REQ
- NmGetCfInfo_1.IND -> AlPgnRxNew8B.install
- AlPgnRxNew8B.IND -> STRUCT_DEMUX.REQ

**Data Connections:**

- NmGetCfInfo_1.sNameField -> STRUCT_DEMUX_3.IN
- NmGetCfInfo_1.sNetEv -> STRUCT_DEMUX_5.IN
- NmGetCfInfo_1.sCfInfo -> STRUCT_DEMUX_4.IN
- NmGetCfInfo_1.sNetEv -> AlPgnRxNew8B.NmSource
- AlPgnRxNew8B.Data -> STRUCT_DEMUX.IN

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_135_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
