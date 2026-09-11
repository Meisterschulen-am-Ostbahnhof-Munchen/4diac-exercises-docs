# Uebung_039b_AX: Spiegelabfolge V2 mit Schrittkette

This article describes the 4diac IDE sub-application Uebung_039b_AX (Spiegelabfolge V2 mit Schrittkette).

----

![Uebung_039b_AX_network](./Uebung_039b_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Spiegelabfolge V2 mit Schrittkette**

-----

## Description and Components

The exercise consists of the sub-application Uebung_039b_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **SoftKey_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IXA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
- **E_TP_Q1**: Instance of type adapter::events::unidirectional::timers::AX_TP.
  - Parameter PT = T#8s
- **E_TP_Q2**: Instance of type adapter::events::unidirectional::timers::AX_TP.
  - Parameter PT = T#4s
- **E_TON**: Instance of type adapter::events::unidirectional::timers::AX_TON.
  - Parameter PT = T#2s

### Connections and Interfaces

**Adapter Connections:**

- SoftKey_F1.IN -> E_TP_Q1.IN
- E_TON.Q -> E_TP_Q2.IN
- E_TP_Q1.Q -> E_TON.IN
- E_TP_Q1.Q -> DigitalOutput_Q1.OUT
- E_TP_Q2.Q -> DigitalOutput_Q2.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_039b_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
