# Uebung_001g: DigitalInput_I1 negiert mit INIT und Delay auf DigitalOutput_Q1

This article describes the 4diac IDE sub-application Uebung_001g (DigitalInput_I1 negiert mit INIT und Delay auf DigitalOutput_Q1).

----

![Uebung_001g_network](./Uebung_001g_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **DigitalInput_I1 negiert mit INIT und Delay auf DigitalOutput_Q1**

-----

## Description and Components

The exercise consists of the sub-application Uebung_001g.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **AX_NOT_INIT**: Instance of type iec61131::booleanOperators::F_NOT_BOOL_INIT.
- **E_DELAY**: Instance of type iec61499::events::E_DELAY.
  - Parameter DT = T#3s

### Connections and Interfaces

**Event Connections:**
- DigitalInput_I1.INITO -> E_DELAY.START
- E_DELAY.EO -> AX_NOT_INIT.INIT
- AX_NOT_INIT.CNF -> DigitalOutput_Q1.REQ
- DigitalInput_I1.IND -> AX_NOT_INIT.REQ

**Data Connections:**
- AX_NOT_INIT.OUT -> DigitalOutput_Q1.OUT
- DigitalInput_I1.IN -> AX_NOT_INIT.IN

### Notes from the Model

> obwohl I1 nicht abgefragt wird beim BOOT, wird AX_NOT hier TRUE ausgeben.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_001g provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
