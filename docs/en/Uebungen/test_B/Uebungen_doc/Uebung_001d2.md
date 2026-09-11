# Uebung_001d2: DigitalInput_I1/2 auf DigitalOutput_Q1/2, als Alternative(Verriegelt) mit Interlock, ohne ECC

This article describes the 4diac IDE sub-application Uebung_001d2 (DigitalInput_I1/2 auf DigitalOutput_Q1/2, als Alternative(Verriegelt) mit Interlock, ohne ECC).

----

![Uebung_001d2_network](./Uebung_001d2_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **DigitalInput_I1/2 auf DigitalOutput_Q1/2, als Alternative(Verriegelt) mit Interlock, ohne ECC**

-----

## Description and Components

The exercise consists of the sub-application Uebung_001d2.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instance of type logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **ILOCK_SWITCH**: Instance of type logiBUS::signalprocessing::interlock::ILOCK_SWITCH.
- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2

### Connections and Interfaces

**Event Connections:**

- DigitalInput_I1.IND -> ILOCK_SWITCH.EI_UP
- DigitalInput_I2.IND -> ILOCK_SWITCH.EI_DOWN
- ILOCK_SWITCH.EO_UP -> DigitalOutput_Q1.REQ
- ILOCK_SWITCH.EO_DOWN -> DigitalOutput_Q2.REQ

**Data Connections:**

- DigitalInput_I1.IN -> ILOCK_SWITCH.DI_UP
- DigitalInput_I2.IN -> ILOCK_SWITCH.DI_DOWN
- ILOCK_SWITCH.DO_UP -> DigitalOutput_Q1.OUT
- ILOCK_SWITCH.DO_DOWN -> DigitalOutput_Q2.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_001d2 provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
