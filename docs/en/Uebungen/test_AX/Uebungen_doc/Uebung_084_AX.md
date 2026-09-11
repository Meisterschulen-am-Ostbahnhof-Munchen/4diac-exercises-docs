# Uebung_084_AX: Beispiel für E_CYCLE

This article describes the 4diac IDE sub-application Uebung_084_AX (Beispiel für E_CYCLE).

----

![Uebung_084_AX_network](./Uebung_084_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_CYCLE**

-----

## Description and Components

The exercise consists of the sub-application Uebung_084_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **AUI_CTU**: Instance of type adapter::events::unidirectional::AUI_CTU.
- **E_CYCLE**: Instance of type iec61499::events::E_CYCLE.
  - Parameter DT = T#1s
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK

### Connections and Interfaces

**Adapter Connections:**
- AUI_CTU.Q -> DigitalOutput_Q1.OUT

**Event Connections:**
- E_CYCLE.EO -> AUI_CTU.CU
- DigitalInput_CLK_I1.IND -> E_CYCLE.START
- DigitalInput_CLK_I2.IND -> E_CYCLE.STOP
- DigitalInput_CLK_I3.IND -> AUI_CTU.R

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_084_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
