# Uebung_024_AX: Spiegelabfolge (4)

This article describes the 4diac IDE sub-application Uebung_024_AX (Spiegelabfolge (4)).

----

![Uebung_024_AX_network](./Uebung_024_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Spiegelabfolge (4)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_024_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **E_SR_Ausfahren_Cyl_1**: Instance of type adapter::events::unidirectional::AX_SR.
- **SoftKey_F2_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_PRESSED
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **E_SR_Ausfahren_Cyl_2**: Instance of type adapter::events::unidirectional::AX_SR.
- **SoftKey_F3_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F9_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F9
  - Parameter InputEvent = SK_PRESSED
- **E_SR_Einfahren_Cyl_2**: Instance of type adapter::events::unidirectional::AX_SR.
- **DigitalOutput_Q3**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalOutput_Q4**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **E_SR_Einfahren_Cyl_1**: Instance of type adapter::events::unidirectional::AX_SR.
- **SoftKey_F8_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F8
  - Parameter InputEvent = SK_PRESSED
- **E_DELAY**: Instance of type iec61499::events::E_DELAY.
  - Parameter DT = T#2s

### Connections and Interfaces

**Adapter Connections:**
- E_SR_Ausfahren_Cyl_1.Q -> DigitalOutput_Q1.OUT
- E_SR_Ausfahren_Cyl_2.Q -> DigitalOutput_Q2.OUT
- E_SR_Einfahren_Cyl_1.Q -> DigitalOutput_Q4.OUT
- E_SR_Einfahren_Cyl_2.Q -> DigitalOutput_Q3.OUT

**Event Connections:**
- SoftKey_UP_F1.IND -> E_SR_Ausfahren_Cyl_1.S
- SoftKey_F2_DOWN.IND -> E_SR_Ausfahren_Cyl_1.R
- SoftKey_F2_DOWN.IND -> E_SR_Ausfahren_Cyl_2.S
- SoftKey_F3_DOWN.IND -> E_SR_Ausfahren_Cyl_2.R
- SoftKey_F8_DOWN.IND -> E_SR_Einfahren_Cyl_1.S
- SoftKey_F9_DOWN.IND -> E_SR_Einfahren_Cyl_1.R
- SoftKey_F8_DOWN.IND -> E_SR_Einfahren_Cyl_2.R
- E_DELAY.EO -> E_SR_Einfahren_Cyl_2.S
- SoftKey_F3_DOWN.IND -> E_DELAY.START

### Notes from the Model

> START-Knopf Ausfahren
> Endlage Ausfahren_Cyl_1
> Endlage Ausfahren_Cyl_2
> Ausfahren_Cyl_1
> Ausfahren_Cyl_2
> Einfahren Zeit gesteuert
> Endlage Einfahren_Cyl_1
> Einfahren_Cyl_2
> Endlage Einfahren_Cyl_2
> Einfahren_Cyl_1

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_024_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
