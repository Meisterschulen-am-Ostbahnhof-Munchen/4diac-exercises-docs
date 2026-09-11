# Uebung_004b3_AX: Toggle Flip-Flop mit IE / Split / Verriegelt

This article describes the 4diac IDE sub-application Uebung_004b3_AX (Toggle Flip-Flop mit IE / Split / Verriegelt).

----

![Uebung_004b3_AX_network](./Uebung_004b3_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Toggle Flip-Flop mit IE / Split / Verriegelt**

-----

## Description and Components

The exercise consists of the sub-application Uebung_004b3_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_SR_I1**: Instance of type adapter::events::unidirectional::AX_SR.
- **E_SWITCH_I1**: Instance of type adapter::events::unidirectional::AX_E_SWITCH.
- **E_SWITCH_I2**: Instance of type adapter::events::unidirectional::AX_E_SWITCH.
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **E_SR_I2**: Instance of type adapter::events::unidirectional::AX_SR.
- **AX_SPLIT_2_1**: Instance of type adapter::events::unidirectional::AX_SPLIT_2.
- **AX_SPLIT_2_2**: Instance of type adapter::events::unidirectional::AX_SPLIT_2.
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK

### Connections and Interfaces

**Adapter Connections:**

- E_SR_I1.Q -> AX_SPLIT_2_1.IN
- AX_SPLIT_2_1.OUT1 -> DigitalOutput_Q1.OUT
- AX_SPLIT_2_1.OUT2 -> E_SWITCH_I1.G
- E_SR_I2.Q -> AX_SPLIT_2_2.IN
- AX_SPLIT_2_2.OUT1 -> DigitalOutput_Q2.OUT
- AX_SPLIT_2_2.OUT2 -> E_SWITCH_I2.G

**Event Connections:**

- DigitalInput_CLK_I1.IND -> E_SWITCH_I1.EI
- E_SWITCH_I1.EO0 -> E_SR_I1.S
- E_SWITCH_I1.EO1 -> E_SR_I1.R
- E_SWITCH_I2.EO1 -> E_SR_I2.R
- E_SWITCH_I2.EO0 -> E_SR_I2.S
- DigitalInput_CLK_I2.IND -> E_SWITCH_I2.EI
- E_SWITCH_I2.EO0 -> E_SR_I1.R
- E_SWITCH_I1.EO0 -> E_SR_I2.R

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_004b3_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
