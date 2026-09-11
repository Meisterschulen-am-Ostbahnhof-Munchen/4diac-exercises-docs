# Uebung_089_AX: Beispiel für E_R_TRIG, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_089_AX (Beispiel für E_R_TRIG, mit Plug and Socket).

----

![Uebung_089_AX_network](./Uebung_089_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_R_TRIG, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_089_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **AX_OR_2**: Instance of type adapter::booleanOperators::AX_OR_2.
- **SPLIT_1**: Instance of type adapter::events::unidirectional::AX_SPLIT_2.
- **DigitalInput_I2**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **AX_R_TRIG**: Instance of type adapter::events::unidirectional::AX_R_TRIG.
- **AX_T_FF_Q1**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **AX_T_FF_1_Q2**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **AX_E_SWITCH**: Instance of type adapter::events::unidirectional::AX_E_SWITCH.

### Connections and Interfaces

**Adapter Connections:**

- DigitalInput_I1.IN -> AX_OR_2.IN1
- DigitalInput_I2.IN -> AX_OR_2.IN2
- AX_T_FF_Q1.Q -> DigitalOutput_Q1.OUT
- AX_T_FF_1_Q2.Q -> DigitalOutput_Q2.OUT
- AX_OR_2.OUT -> SPLIT_1.IN
- SPLIT_1.OUT1 -> AX_R_TRIG.QI
- SPLIT_1.OUT2 -> AX_E_SWITCH.G

**Event Connections:**

- AX_R_TRIG.EO -> AX_T_FF_Q1.CLK
- AX_E_SWITCH.EO1 -> AX_T_FF_1_Q2.CLK

### Notes from the Model

> R_TRIG schaltet nur wenn wirklich steigende Flanke
> E_SWITCH schaltet auch, wenn anderweitig ein Event kommt.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_089_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
