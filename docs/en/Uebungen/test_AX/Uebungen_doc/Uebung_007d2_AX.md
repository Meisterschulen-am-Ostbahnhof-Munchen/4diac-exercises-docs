# Uebung_007d2_AX: Blinker mit E_CYCLE, FB_AR_RANDOM, AR_GT und AX_D_FF_TMIN

This article describes the 4diac IDE sub-application Uebung_007d2_AX (Blinker mit E_CYCLE, FB_AR_RANDOM, AR_GT und AX_D_FF_TMIN).

----

![Uebung_007d2_AX_network](./Uebung_007d2_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Blinker mit E_CYCLE, FB_AR_RANDOM, AR_GT und AX_D_FF_TMIN**

-----

## Description and Components

The exercise consists of the sub-application Uebung_007d2_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **E_CYCLE**: Instance of type iec61499::events::E_CYCLE.
  - Parameter DT = T#1ms
- **FB_AR_RANDOM**: Instance of type adapter::utils::FB_AR_RANDOM.
  - Parameter SEED = 0
- **initval_AR**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#0.49
- **AR_GT**: Instance of type adapter::iec61131::comparison::AR_GT.
- **AX_D_FF_TMIN**: Instance of type adapter::events::unidirectional::AX_D_FF_TMIN.
  - Parameter Tmin = T#3s
- **INIT**: Instance of type iec61131::booleanOperators::INIT.

### Connections and Interfaces

**Adapter Connections:**
- FB_AR_RANDOM.OUT -> AR_GT.IN1
- initval_AR.OUT -> AR_GT.IN2
- AR_GT.OUT -> AX_D_FF_TMIN.I
- AX_D_FF_TMIN.Q -> DigitalOutput_Q1.OUT

**Event Connections:**
- INIT.INITO -> INIT.REQ
- INIT.CNF -> E_CYCLE.START
- E_CYCLE.EO -> FB_AR_RANDOM.REQ

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_007d2_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
