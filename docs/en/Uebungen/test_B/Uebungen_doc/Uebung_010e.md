# Uebung_010e: SR und T-Flip-Flop mit 3x SoftKey (Softkey_IE, SK_RELEASED) mit GreenWhiteBackground am Toggle-SoftKey

This article describes the 4diac IDE sub-application Uebung_010e (SR und T-Flip-Flop mit 3x SoftKey (Softkey_IE, SK_RELEASED) mit GreenWhiteBackground am Toggle-SoftKey).

----

![Uebung_010e_network](./Uebung_010e_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **SR und T-Flip-Flop mit 3x SoftKey (Softkey_IE, SK_RELEASED) mit GreenWhiteBackground am Toggle-SoftKey**

-----

## Description and Components

The exercise consists of the sub-application Uebung_010e.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **SoftKey_SET**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_RESET**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_TOGGLE**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_RELEASED
- **AX_T_FF_SR**: Instance of type iec61499::events::E_T_FF_SR.

### Connections and Interfaces

**Event Connections:**
- SoftKey_SET.IND -> AX_T_FF_SR.S
- SoftKey_RESET.IND -> AX_T_FF_SR.R
- SoftKey_TOGGLE.IND -> AX_T_FF_SR.CLK
- AX_T_FF_SR.EO -> DigitalOutput_Q1.REQ
- AX_T_FF_SR.EO -> GreenWhiteBackground_AX.REQ

**Data Connections:**
- AX_T_FF_SR.Q -> DigitalOutput_Q1.OUT
- AX_T_FF_SR.Q -> GreenWhiteBackground_AX.DI1

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_010e provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
