# Uebung_052_AX: DigitalInput_I1-_I4 auf DigitalOutput_Q1-_Q4, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_052_AX (DigitalInput_I1-_I4 auf DigitalOutput_Q1-_Q4, mit Plug and Socket).

----

![Uebung_052_AX_network](./Uebung_052_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **DigitalInput_I1-_I4 auf DigitalOutput_Q1-_Q4, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_052_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **DigitalInput_I3**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **DigitalOutput_Q4**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I4**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
- **GET_STRUCT_VALUE**: Instance of type eclipse4diac::convert::GET_STRUCT_VALUE.
  - Parameter member = 'X_00'
- **STRUCT_MUX**: Instance of type eclipse4diac::convert::STRUCT_MUX.
- **AX_X_TO_BOOL_1**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_2**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_3**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_4**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_BOOL_TO_X**: Instance of type adapter::conversion::unidirectional::AX_BOOL_TO_X.

### Connections and Interfaces

**Adapter Connections:**

- DigitalInput_I1.IN -> AX_X_TO_BOOL_1.AX_IN
- DigitalInput_I2.IN -> AX_X_TO_BOOL_2.AX_IN
- DigitalInput_I3.IN -> AX_X_TO_BOOL_3.AX_IN
- DigitalInput_I4.IN -> AX_X_TO_BOOL_4.AX_IN
- AX_BOOL_TO_X.AX_OUT -> DigitalOutput_Q4.OUT

**Event Connections:**

- GET_STRUCT_VALUE.CNF -> AX_BOOL_TO_X.REQ
- AX_X_TO_BOOL_1.CNF -> STRUCT_MUX.REQ
- AX_X_TO_BOOL_2.CNF -> STRUCT_MUX.REQ
- AX_X_TO_BOOL_3.CNF -> STRUCT_MUX.REQ
- AX_X_TO_BOOL_4.CNF -> STRUCT_MUX.REQ
- STRUCT_MUX.CNF -> GET_STRUCT_VALUE.REQ

**Data Connections:**

- GET_STRUCT_VALUE.output -> AX_BOOL_TO_X.OUT
- AX_X_TO_BOOL_1.IN -> STRUCT_MUX.X_00
- AX_X_TO_BOOL_2.IN -> STRUCT_MUX.X_01
- AX_X_TO_BOOL_3.IN -> STRUCT_MUX.X_02
- AX_X_TO_BOOL_4.IN -> STRUCT_MUX.X_03
- STRUCT_MUX.OUT -> GET_STRUCT_VALUE.in_struct

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_052_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
