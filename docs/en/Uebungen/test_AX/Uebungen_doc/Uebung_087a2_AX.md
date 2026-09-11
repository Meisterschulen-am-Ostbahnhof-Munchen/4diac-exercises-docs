# Uebung_087a2_AX: Beispiel für E_DEMUX_4, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_087a2_AX (Beispiel für E_DEMUX_4, mit Plug and Socket).

----

![Uebung_087a2_AX_network](./Uebung_087a2_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_DEMUX_4, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_087a2_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalInput_I2**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **DigitalOutput_Q3**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalInput_I3**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **DigitalOutput_Q4**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **DigitalInput_I4**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
- **E_DEMUX_4**: Instance of type iec61499::events::E_DEMUX_4.
- **E_MUX_4**: Instance of type iec61499::events::E_MUX_4.
- **AX_X_TO_BOOL_1**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_2**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_3**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_4**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_BOOL_TO_X_1**: Instance of type adapter::conversion::unidirectional::AX_BOOL_TO_X.
- **AX_BOOL_TO_X_2**: Instance of type adapter::conversion::unidirectional::AX_BOOL_TO_X.
- **AX_BOOL_TO_X_3**: Instance of type adapter::conversion::unidirectional::AX_BOOL_TO_X.
- **AX_BOOL_TO_X_4**: Instance of type adapter::conversion::unidirectional::AX_BOOL_TO_X.

### Connections and Interfaces

**Adapter Connections:**

- DigitalInput_I1.IN -> AX_X_TO_BOOL_1.AX_IN
- DigitalInput_I2.IN -> AX_X_TO_BOOL_2.AX_IN
- DigitalInput_I3.IN -> AX_X_TO_BOOL_3.AX_IN
- DigitalInput_I4.IN -> AX_X_TO_BOOL_4.AX_IN
- AX_BOOL_TO_X_1.AX_OUT -> DigitalOutput_Q1.OUT
- AX_BOOL_TO_X_2.AX_OUT -> DigitalOutput_Q2.OUT
- AX_BOOL_TO_X_3.AX_OUT -> DigitalOutput_Q3.OUT
- AX_BOOL_TO_X_4.AX_OUT -> DigitalOutput_Q4.OUT

**Event Connections:**

- E_MUX_4.EO -> E_DEMUX_4.EI
- AX_X_TO_BOOL_1.CNF -> E_MUX_4.EI1
- AX_X_TO_BOOL_2.CNF -> E_MUX_4.EI2
- AX_X_TO_BOOL_3.CNF -> E_MUX_4.EI3
- AX_X_TO_BOOL_4.CNF -> E_MUX_4.EI4
- E_DEMUX_4.EO4 -> AX_BOOL_TO_X_4.REQ
- E_DEMUX_4.EO3 -> AX_BOOL_TO_X_3.REQ
- E_DEMUX_4.EO2 -> AX_BOOL_TO_X_2.REQ
- E_DEMUX_4.EO1 -> AX_BOOL_TO_X_1.REQ

**Data Connections:**

- E_MUX_4.K -> E_DEMUX_4.K
- AX_X_TO_BOOL_1.IN -> AX_BOOL_TO_X_1.OUT
- AX_X_TO_BOOL_2.IN -> AX_BOOL_TO_X_2.OUT
- AX_X_TO_BOOL_3.IN -> AX_BOOL_TO_X_3.OUT
- AX_X_TO_BOOL_4.IN -> AX_BOOL_TO_X_4.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_087a2_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
