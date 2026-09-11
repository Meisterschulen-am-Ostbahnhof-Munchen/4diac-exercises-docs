# Uebung_039a_AX: Spiegelabfolge V2 mit Schrittkette

This article describes the 4diac IDE sub-application Uebung_039a_AX (Spiegelabfolge V2 mit Schrittkette).

----

![Uebung_039a_AX_network](./Uebung_039a_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Spiegelabfolge V2 mit Schrittkette**

-----

## Description and Components

The exercise consists of the sub-application Uebung_039a_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **E_TimeOut**: Instance of type iec61499::events::E_TimeOut.
- **DigitalInput_DOWN_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_DOWN_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_DOWN_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_DOWN_I4**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **sequence_05**: Instance of type logiBUS::utils::sequence::combi::sequence_ET_05.
  - Parameter DT_S1_S2 = NO_TIME
  - Parameter DT_S2_S3 = NO_TIME
  - Parameter DT_S3_S4 = T#5s
  - Parameter DT_S4_S5 = NO_TIME
  - Parameter DT_S5_START = NO_TIME
- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED

### Connections and Interfaces

**Adapter Connections:**

- sequence_05.timeOut -> E_TimeOut.TimeOutSocket

**Event Connections:**

- DigitalInput_DOWN_I1.IND -> sequence_05.S1_S2
- DigitalInput_DOWN_I2.IND -> sequence_05.S2_S3
- DigitalInput_DOWN_I3.IND -> sequence_05.S4_S5
- DigitalInput_DOWN_I4.IND -> sequence_05.S5_START
- sequence_05.CNF -> NumbAnzeig.CNF
- SoftKey_UP_F1.IND -> sequence_05.START_S1
- sequence_05.EO_S1 -> Q1.SET
- sequence_05.EO_S2 -> Q2.SET
- sequence_05.EO_S4 -> Q2.RESET
- sequence_05.EO_S5 -> Q1.RESET

**Data Connections:**

- sequence_05.STATE_NR -> NumbAnzeig.NewValue

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_039a_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
