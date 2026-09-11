# Uebung_007d_AX: Blinker mit E_CYCLE und E_T_FF

This article describes the 4diac IDE sub-application Uebung_007d_AX (Blinker mit E_CYCLE und E_T_FF).

----

![Uebung_007d_AX_network](./Uebung_007d_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Blinker mit E_CYCLE und E_T_FF**

-----

## Description and Components

The exercise consists of the sub-application Uebung_007d_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **E_CYCLE**: Instance of type iec61499::events::E_CYCLE.
  - Parameter DT = T#1ms
- **E_T_FF**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **E_TMIN**: Instance of type iec61499::events::E_TMIN.
  - Parameter Tmin = T#10s
- **INIT**: Instance of type iec61131::booleanOperators::INIT.

### Connections and Interfaces

**Adapter Connections:**
- E_T_FF.Q -> DigitalOutput_Q1.OUT

**Event Connections:**
- E_CYCLE.EO -> E_TMIN.EI
- E_TMIN.EO -> E_T_FF.CLK
- INIT.INITO -> INIT.REQ
- INIT.CNF -> E_CYCLE.START

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_007d_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
