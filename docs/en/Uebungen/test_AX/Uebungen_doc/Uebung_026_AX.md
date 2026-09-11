# Uebung_026_AX: Spiegelabfolge (6)

This article describes the 4diac IDE sub-application Uebung_026_AX (Spiegelabfolge (6)).

----

![Uebung_026_AX_network](./Uebung_026_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Spiegelabfolge (6)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_026_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_F2_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F3_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F9_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F9
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F8_DOWN**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F8
  - Parameter InputEvent = SK_PRESSED
- **E_DELAY**: Instance of type iec61499::events::E_DELAY.
  - Parameter DT = T#2s
- **E_REND_Ausfahren_Cyl_1**: Instance of type iec61499::events::E_REND.
- **E_REND_Ausfahren_Cyl_2**: Instance of type iec61499::events::E_REND.
- **E_REND_Einfahren_Cyl_2**: Instance of type iec61499::events::E_REND.
- **E_REND_Einfahren_Cyl_1**: Instance of type iec61499::events::E_REND.

### Connections and Interfaces

**Event Connections:**

- SoftKey_F2_DOWN.IND -> E_REND_Ausfahren_Cyl_1.EI2
- SoftKey_F3_DOWN.IND -> E_REND_Ausfahren_Cyl_2.EI2
- SoftKey_F9_DOWN.IND -> E_REND_Einfahren_Cyl_1.EI2
- SoftKey_F8_DOWN.IND -> E_REND_Einfahren_Cyl_2.EI2
- E_REND_Ausfahren_Cyl_2.EO -> E_DELAY.START
- SoftKey_UP_F1.IND -> E_REND_Ausfahren_Cyl_1.R
- SoftKey_F2_DOWN.IND -> E_REND_Ausfahren_Cyl_2.R
- E_DELAY.EO -> E_REND_Einfahren_Cyl_2.R
- SoftKey_F8_DOWN.IND -> E_REND_Einfahren_Cyl_1.R
- E_REND_Einfahren_Cyl_2.EO -> Q4.SET
- E_REND_Einfahren_Cyl_1.EO -> Q4.RESET
- Q4.EO1 -> E_REND_Einfahren_Cyl_1.EI1
- E_REND_Ausfahren_Cyl_1.EO -> Q2.SET
- E_REND_Ausfahren_Cyl_2.EO -> Q2.RESET
- Q2.EO1 -> E_REND_Ausfahren_Cyl_2.EI1
- E_DELAY.EO -> Q3.SET
- E_REND_Einfahren_Cyl_2.EO -> Q3.RESET
- Q3.EO1 -> E_REND_Einfahren_Cyl_2.EI1
- SoftKey_UP_F1.IND -> Q1.SET
- E_REND_Ausfahren_Cyl_1.EO -> Q1.RESET
- Q1.EO1 -> E_REND_Ausfahren_Cyl_1.EI1

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

Exercise Uebung_026_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
