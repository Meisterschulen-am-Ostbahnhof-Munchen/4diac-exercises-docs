# Uebung_089a_AX: Beispiel für E_RF_TRIG, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_089a_AX (Beispiel für E_RF_TRIG, mit Plug and Socket).

----

![Uebung_089a_AX_network](./Uebung_089a_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_RF_TRIG, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_089a_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **AX_RF_TRIG**: Instance of type adapter::events::unidirectional::AX_RF_TRIG.
- **AX_T_FF_Q1**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **AX_T_FF_Q2**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2

### Connections and Interfaces

**Adapter Connections:**
- DigitalInput_I1.IN -> AX_RF_TRIG.QI
- AX_T_FF_Q1.Q -> DigitalOutput_Q1.OUT
- AX_T_FF_Q2.Q -> DigitalOutput_Q2.OUT

**Event Connections:**
- AX_RF_TRIG.ER -> AX_T_FF_Q1.CLK
- AX_RF_TRIG.EF -> AX_T_FF_Q2.CLK

### Notes from the Model

> Ein einziger Eingang, EIN Signal - AX_RF_TRIG liefert beide Flanken gleichzeitig (ER=steigend, EF=fallend). Q1 togglet bei steigender, Q2 bei fallender Flanke.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_089a_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
