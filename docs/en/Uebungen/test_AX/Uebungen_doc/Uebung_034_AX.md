# Uebung_034_AX: Analog-Eingang auf PWM Ausgang (Adapter Version)

This article describes the 4diac IDE sub-application Uebung_034_AX (Analog-Eingang auf PWM Ausgang (Adapter Version)).

----

![Uebung_034_AX_network](./Uebung_034_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Analog-Eingang auf PWM Ausgang (Adapter Version)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_034_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **AnalogInput_I7**: Instance of type logiBUS::io::AI::logiBUS_AI_IDA.
  - Parameter QI = TRUE
  - Parameter Input = logiBUS_AI::AnalogInput_I7
  - Parameter AnalogInput_hysteresis = 50
- **PWMOutput_Q4**: Instance of type logiBUS::io::DQ::logiBUS_QDA_PWM.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **AD_SHL**: Instance of type adapter::iec61131::bitwise::AD_SHL.
  - Parameter N = UINT#1

### Connections and Interfaces

**Adapter Connections:**

- AnalogInput_I7.IN -> AD_SHL.IN
- AD_SHL.OUT -> PWMOutput_Q4.OUT

**Event Connections:**

- AnalogInput_I7.INITO -> PWMOutput_Q4.INIT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_034_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
