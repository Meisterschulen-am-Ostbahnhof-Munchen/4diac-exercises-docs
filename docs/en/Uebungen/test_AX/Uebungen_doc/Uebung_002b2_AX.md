# Uebung_002b2_AX: DigitalInput_I1/_I2/_I3 auf DigitalOutput_Q1; mit AX Adapter

This article describes the 4diac IDE sub-application Uebung_002b2_AX (DigitalInput_I1/_I2/_I3 auf DigitalOutput_Q1; mit AX Adapter).

----

![Uebung_002b2_AX_network](./Uebung_002b2_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **DigitalInput_I1/_I2/_I3 auf DigitalOutput_Q1; mit AX Adapter**

-----

## Description and Components

The exercise consists of the sub-application Uebung_002b2_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **AX_AND_2**: Instance of type adapter::booleanOperators::AX_AND_2.
- **DigitalInput_I3**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **AX_OR_2**: Instance of type adapter::booleanOperators::AX_OR_2.

### Connections and Interfaces

**Adapter Connections:**

- DigitalInput_I1.IN -> AX_AND_2.IN1
- DigitalInput_I2.IN -> AX_AND_2.IN2
- DigitalInput_I3.IN -> AX_OR_2.IN2
- AX_AND_2.OUT -> AX_OR_2.IN1
- AX_OR_2.OUT -> DigitalOutput_Q1.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_002b2_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
