# Uebung_019c_AX: Umschalten einer Maske, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_019c_AX (Umschalten einer Maske, mit Plug and Socket).

----

![Uebung_019c_AX_network](./Uebung_019c_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Umschalten einer Maske, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_019c_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **Q_ActiveMask**: Instance of type isobus::UT::Q::Q_ActiveMask_AUI.
- **ACK**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AX_SR**: Instance of type adapter::events::unidirectional::AX_SR.
- **Alarmausgang**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q8
- **Alarmeingang**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **AX_R_TRIG_Alarm**: Instance of type adapter::events::unidirectional::AX_R_TRIG.
- **AX_SPLIT_4**: Instance of type adapter::events::unidirectional::AX_SPLIT_2.

### Connections and Interfaces

**Adapter Connections:**

- Select_4_AUI.OUT -> Q_ActiveMask.u16NewMaskId
- AX_SR.Q -> Alarmausgang.OUT
- Alarmeingang.IN -> AX_SPLIT_4.IN
- AX_SPLIT_4.OUT1 -> AX_E_PERMIT_INVERT_1.PERMIT
- AX_SPLIT_4.OUT2 -> AX_R_TRIG_Alarm.QI

**Event Connections:**

- AX_R_TRIG_Alarm.EO -> AX_SR.S
- ACK.IND -> AX_E_PERMIT_INVERT_1.EI3
- DigitalInput_CLK_I1.IND -> AX_E_PERMIT_INVERT_1.EI1
- DigitalInput_CLK_I2.IND -> AX_E_PERMIT_INVERT_1.EI2
- AX_E_PERMIT_INVERT_1.EO3 -> AX_SR.R
- AX_E_PERMIT_INVERT_1.EO3 -> Select_4_AUI.EI3
- AX_E_PERMIT_INVERT_1.EO2 -> Select_4_AUI.EI2
- AX_E_PERMIT_INVERT_1.EO1 -> Select_4_AUI.EI1

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_019c_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
