# Uebung_070_AX: WBSD auf UT ausgeben

This article describes the 4diac IDE sub-application Uebung_070_AX (WBSD auf UT ausgeben).

----

![Uebung_070_AX_network](./Uebung_070_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **WBSD auf UT ausgeben**

-----

## Description and Components

The exercise consists of the sub-application Uebung_070_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **I_WBSD**: Instance of type isobus::tecu::IA_WBSD.
  - Parameter QI = TRUE
- **Q_NumericValue**: Instance of type isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = NumberVariable_Wheel_based_machine_speed
- **F_UINT_TO_UDINT**: Instance of type adapter::conversion::unidirectional::AUI_TO_AUDI.

### Connections and Interfaces

**Adapter Connections:**
- I_WBSD.SPEED -> F_UINT_TO_UDINT.AUI_IN
- F_UINT_TO_UDINT.AUDI_OUT -> Q_NumericValue.u32NewValue

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_070_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
