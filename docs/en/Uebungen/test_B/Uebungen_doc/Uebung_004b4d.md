# Uebung_004b4d: Drei gegenseitig verriegelte Toggle-Flip-Flops in einer Kette via AE2-Adapter mit ILOCK_T_FF

This article describes the 4diac IDE sub-application Uebung_004b4d (Drei gegenseitig verriegelte Toggle-Flip-Flops in einer Kette via AE2-Adapter mit ILOCK_T_FF).

----

![Uebung_004b4d_network](./Uebung_004b4d_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Drei gegenseitig verriegelte Toggle-Flip-Flops in einer Kette via AE2-Adapter mit ILOCK_T_FF**

-----

## Description and Components

The exercise consists of the sub-application Uebung_004b4d.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **ILOCK_T_FF1**: Instance of type logiBUS::signalprocessing::interlock::ILOCK_T_FF.
- **ILOCK_T_FF2**: Instance of type logiBUS::signalprocessing::interlock::ILOCK_T_FF.
- **ILOCK_T_FF3**: Instance of type logiBUS::signalprocessing::interlock::ILOCK_T_FF.
- **DigitalOutput_Q3**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalInput_CLK_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK

### Connections and Interfaces

**Adapter Connections:**
- ILOCK_T_FF1.ILOCK_OUT -> ILOCK_T_FF2.ILOCK_IN
- ILOCK_T_FF2.ILOCK_OUT -> ILOCK_T_FF3.ILOCK_IN

**Event Connections:**
- DigitalInput_CLK_I1.IND -> ILOCK_T_FF1.CLK
- DigitalInput_CLK_I2.IND -> ILOCK_T_FF2.CLK
- DigitalInput_CLK_I3.IND -> ILOCK_T_FF3.CLK
- ILOCK_T_FF1.EO -> DigitalOutput_Q1.REQ
- ILOCK_T_FF2.EO -> DigitalOutput_Q2.REQ
- ILOCK_T_FF3.EO -> DigitalOutput_Q3.REQ

**Data Connections:**
- ILOCK_T_FF1.Q -> DigitalOutput_Q1.OUT
- ILOCK_T_FF2.Q -> DigitalOutput_Q2.OUT
- ILOCK_T_FF3.Q -> DigitalOutput_Q3.OUT

### Notes from the Model

> durch den Einsatz eines Bidirektionalen Adapters: 1 Verbindung REICHT !

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_004b4d provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
