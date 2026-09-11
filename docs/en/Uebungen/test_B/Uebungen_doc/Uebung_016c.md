# Uebung_016c: Background Colour umschalten -- mit SubApp für Green/White Background

This article describes the 4diac IDE sub-application Uebung_016c (Background Colour umschalten -- mit SubApp für Green/White Background).

----

![Uebung_016c_network](./Uebung_016c_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Background Colour umschalten -- mit SubApp für Green/White Background**

-----

## Description and Components

The exercise consists of the sub-application Uebung_016c.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_UP_F2**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_RELEASED
- **E_SR**: Instance of type iec61499::events::E_SR.

### Connections and Interfaces

**Event Connections:**
- SoftKey_UP_F1.IND -> E_SR.S
- SoftKey_UP_F2.IND -> E_SR.R
- E_SR.EO -> GreenWhiteBackground1.REQ

**Data Connections:**
- E_SR.Q -> GreenWhiteBackground1.DI1

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_016c provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
