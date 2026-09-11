# Uebung_093_AX: Beispiel für E_TABLE

This article describes the 4diac IDE sub-application Uebung_093_AX (Beispiel für E_TABLE).

----

![Uebung_093_AX_network](./Uebung_093_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_TABLE**

-----

## Description and Components

The exercise consists of the sub-application Uebung_093_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **E_TABLE**: Instance of type iec61499::events::E_TABLE.
  - Parameter DT = [T#0s, T#2s, T#3s, T#4s]
  - Parameter N = 4
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_T_FF**: Instance of type adapter::events::unidirectional::AX_T_FF.

### Connections and Interfaces

**Adapter Connections:**

- E_T_FF.Q -> DigitalOutput_Q1.OUT

**Event Connections:**

- DigitalInput_CLK_I1.IND -> E_TABLE.START
- E_TABLE.EO -> E_T_FF.CLK

### Notes from the Model

> E_TABLE wird 4 Events ausgeben, das erste sofort nach dem Click, das letzte 9s nach dem Click
[T#0s, T#2s, T#3s, T#4s]

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_093_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
