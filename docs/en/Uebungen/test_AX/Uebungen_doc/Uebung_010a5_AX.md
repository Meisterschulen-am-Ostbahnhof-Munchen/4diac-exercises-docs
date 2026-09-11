# Uebung_010a5_AX: SoftKey_F1 auf DigitalOutput_Q1 (Datapanel)

This article describes the 4diac IDE sub-application Uebung_010a5_AX (SoftKey_F1 auf DigitalOutput_Q1 (Datapanel)).

----

![Uebung_010a5_AX_network](./Uebung_010a5_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **SoftKey_F1 auf DigitalOutput_Q1 (Datapanel)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_010a5_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **Input_Power_Port_5**: Instance of type DataPanel::io::MI::DQ::DataPanel_MI_QX.
  - Parameter QI = TRUE
  - Parameter u8SAMember = MI_00
  - Parameter Output = Input_Power_Port_5
- **SoftKey_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IX.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1

### Connections and Interfaces

**Event Connections:**
- SoftKey_F1.IND -> Input_Power_Port_5.REQ

**Data Connections:**
- SoftKey_F1.IN -> Input_Power_Port_5.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_010a5_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
