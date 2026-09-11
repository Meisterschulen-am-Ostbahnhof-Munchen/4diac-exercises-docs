# Uebung_120_AX: Übung zu ISOBUS Name

This article describes the 4diac IDE sub-application Uebung_120_AX (Übung zu ISOBUS Name).

----

![Uebung_120_AX_network](./Uebung_120_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu ISOBUS Name**

-----

## Description and Components

The exercise consists of the sub-application Uebung_120_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **NmGetCfInfo**: Instance of type isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = network
  - Parameter address = ADD_ALL_PASS
  - Parameter mask = FLT_ALL_PASS
- **STRUCT_DEMUX_2**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_3**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **NmSetNameField**: Instance of type isobus::pgn::NmSetNameField.
- **NmSetNameField_1**: Instance of type isobus::pgn::NmSetNameField.
- **STRUCT_DEMUX**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_1**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.

### Connections and Interfaces

**Event Connections:**

- NmGetCfInfo.IND -> STRUCT_DEMUX_3.REQ
- NmGetCfInfo.IND -> STRUCT_DEMUX_2.REQ
- STRUCT_DEMUX_3.CNF -> NmSetNameField.REQ
- STRUCT_DEMUX_2.CNF -> NmSetNameField_1.REQ
- NmSetNameField.CNF -> STRUCT_DEMUX.REQ
- NmSetNameField_1.CNF -> STRUCT_DEMUX_1.REQ

**Data Connections:**

- NmGetCfInfo.sNetEv -> STRUCT_DEMUX_3.IN
- NmGetCfInfo.sCfInfo -> STRUCT_DEMUX_2.IN
- STRUCT_DEMUX_3.cfName -> NmSetNameField.au8IsoName
- STRUCT_DEMUX_2.au8Name -> NmSetNameField_1.au8IsoName
- NmSetNameField. -> STRUCT_DEMUX.IN
- NmSetNameField_1. -> STRUCT_DEMUX_1.IN

### Notes from the Model

> mit einem Event an RSP wird der nächste ACL abgefragt.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_120_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
