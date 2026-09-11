# Uebung_019a_AX: Umschalten einer Maske

This article describes the 4diac IDE sub-application Uebung_019a_AX (Umschalten einer Maske).

----

![Uebung_019a_AX_network](./Uebung_019a_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Umschalten einer Maske**

-----

## Description and Components

The exercise consists of the sub-application Uebung_019a_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **ACK**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = ID_NULL
  - Parameter InputEvent = SK_PRESSED
- **Q_ActiveMask**: Instance of type isobus::UT::Q::Q_ActiveMask_AUI.

### Connections and Interfaces

**Adapter Connections:**
- Select_4_AUI.OUT -> Q_ActiveMask.u16NewMaskId

**Event Connections:**
- DigitalInput_CLK_I1.IND -> Select_4_AUI.EI4
- DigitalInput_CLK_I2.IND -> Select_4_AUI.EI1
- DigitalInput_CLK_I3.IND -> Select_4_AUI.EI2
- ACK.IND -> Select_4_AUI.EI3

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_019a_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
