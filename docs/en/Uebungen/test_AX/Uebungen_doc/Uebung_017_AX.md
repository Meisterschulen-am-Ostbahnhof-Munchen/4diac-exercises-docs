# Uebung_017_AX: Control Audio Signal

This article describes the 4diac IDE sub-application Uebung_017_AX (Control Audio Signal).

----

![Uebung_017_AX_network](./Uebung_017_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Control Audio Signal**

-----

## Description and Components

The exercise consists of the sub-application Uebung_017_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **Q_CtrlAudioSignal**: Instance of type isobus::UT::Q::Q_CtrlAudioSignal.
  - Parameter u8NumOfRepit = 1
  - Parameter u16Frequency = 440
  - Parameter u16OnTimeMs = 150
  - Parameter u16OffTimeMs = 0

### Connections and Interfaces

**Event Connections:**

- SoftKey_UP_F1.IND -> Q_CtrlAudioSignal.REQ

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_017_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
