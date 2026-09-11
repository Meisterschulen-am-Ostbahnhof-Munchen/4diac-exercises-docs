# Uebung_034b_AX: LONG_PRESS_HOLD-Eingang auf PWM Ausgang (Adapter Version) mit Terminal-Ausgabe

This article describes the 4diac IDE sub-application Uebung_034b_AX (LONG_PRESS_HOLD-Eingang auf PWM Ausgang (Adapter Version) mit Terminal-Ausgabe).

----

![Uebung_034b_AX_network](./Uebung_034b_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **LONG_PRESS_HOLD-Eingang auf PWM Ausgang (Adapter Version) mit Terminal-Ausgabe**

-----

## Description and Components

The exercise consists of the sub-application Uebung_034b_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **PWMOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QDA_PWM.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **IE_SPEED_UP**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_LONG_PRESS_HOLD
- **IE_SPEED_DOWN**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_LONG_PRESS_HOLD
- **IE_STOP**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **IE_FULL**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AUDI_CTUD**: Instance of type adapter::events::unidirectional::AUDI_CTUD_UDINT.
- **Q_NumericValue_AUDI**: Instance of type isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = OutputNumber_N1
- **AUDI_SPLIT_2**: Instance of type adapter::events::unidirectional::AUDI_SPLIT_2.
- **AUDI_TO_AD**: Instance of type adapter::conversion::unidirectional::AUDI_TO_AD.

### Connections and Interfaces

**Adapter Connections:**
- AUDI_CTUD.CV -> AUDI_SPLIT_2.IN
- AUDI_SPLIT_2.OUT2 -> Q_NumericValue_AUDI.u32NewValue
- AUDI_SPLIT_2.OUT1 -> AUDI_TO_AD.AUDI_IN
- AUDI_TO_AD.AD_OUT -> PWMOutput_Q1.OUT

**Event Connections:**
- IE_SPEED_DOWN.IND -> AUDI_CTUD.CD
- IE_STOP.IND -> AUDI_CTUD.R
- IE_FULL.IND -> AUDI_CTUD.LD
- IE_SPEED_UP.IND -> AUDI_CTUD.CU

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_034b_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
