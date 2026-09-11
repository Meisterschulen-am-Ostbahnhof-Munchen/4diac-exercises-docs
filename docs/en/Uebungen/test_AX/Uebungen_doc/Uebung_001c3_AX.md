# Uebung_001c3_AX: DigitalInput_I1 auf DigitalOutput_Q1 --> Eingang abfragen bei Boot

This article describes the 4diac IDE sub-application Uebung_001c3_AX (DigitalInput_I1 auf DigitalOutput_Q1 --> Eingang abfragen bei Boot.).

----

![Uebung_001c3_AX_network](./Uebung_001c3_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **DigitalInput_I1 auf DigitalOutput_Q1 --> Eingang abfragen bei Boot.**

-----

## Description and Components

The exercise consists of the sub-application Uebung_001c3_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **AX_NOOP**: Instance of type adapter::booleanOperators::AX_NOOP.

### Connections and Interfaces

**Adapter Connections:**

- DigitalInput_I1.IN -> AX_NOOP.IN
- AX_NOOP.OUT -> DigitalOutput_Q1.OUT

**Event Connections:**

- DigitalInput_I1.INITO -> DigitalInput_I1.REQ

### Notes from the Model

> ohne die Linie INITO->REQ ist der Ausgang Q1 beim Start FALSE. mit der Linie ist er TRUE.
> In Adapter-Architektur entspricht der doppelten Negierung (NOT an NOT) der Baustein AX_NOOP.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_001c3_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
