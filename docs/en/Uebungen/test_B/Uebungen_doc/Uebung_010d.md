# Uebung_010d: Toggle Flip-Flop mit IE SoftKey_F1 SK_RELEASED mit GreenWhiteBackground

This article describes the 4diac IDE sub-application Uebung_010d (Toggle Flip-Flop mit IE SoftKey_F1 SK_RELEASED mit GreenWhiteBackground).

----

![Uebung_010d_network](./Uebung_010d_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Toggle Flip-Flop mit IE SoftKey_F1 SK_RELEASED mit GreenWhiteBackground**

-----

## Description and Components

The exercise consists of the sub-application Uebung_010d.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **AX_T_FF**: Instance of type iec61499::events::E_T_FF.

### Connections and Interfaces

**Event Connections:**

- SoftKey_UP_F1.IND -> AX_T_FF.CLK
- AX_T_FF.EO -> DigitalOutput_Q1.REQ
- AX_T_FF.EO -> GreenWhiteBackground_AX.REQ

**Data Connections:**

- AX_T_FF.Q -> DigitalOutput_Q1.OUT
- AX_T_FF.Q -> GreenWhiteBackground_AX.DI1

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_010d provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
