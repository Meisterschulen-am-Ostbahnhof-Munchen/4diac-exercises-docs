# Uebung_082_AX: Beispiel für E_CTUD, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_082_AX (Beispiel für E_CTUD, mit Plug and Socket).

----

![Uebung_082_AX_network](./Uebung_082_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_CTUD, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_082_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AUI_CTUD**: Instance of type adapter::events::unidirectional::AUI_CTUD.
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I4**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2

### Connections and Interfaces

**Adapter Connections:**

- AUI_CTUD.QU -> DigitalOutput_Q1.OUT
- AUI_CTUD.QD -> DigitalOutput_Q2.OUT

**Event Connections:**

- DigitalInput_CLK_I1.IND -> AUI_CTUD.CU
- DigitalInput_CLK_I2.IND -> AUI_CTUD.CD
- DigitalInput_CLK_I3.IND -> AUI_CTUD.R
- DigitalInput_CLK_I4.IND -> AUI_CTUD.LD

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_082_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
