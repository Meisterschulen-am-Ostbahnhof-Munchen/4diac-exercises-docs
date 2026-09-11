# Uebung_011b1_AX: Numeric Value Input ADD

This article describes the 4diac IDE sub-application Uebung_011b1_AX (Numeric Value Input ADD).

----

![Uebung_011b1_AX_network](./Uebung_011b1_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Numeric Value Input ADD**

-----

## Description and Components

The exercise consists of the sub-application Uebung_011b1_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **InputNumber_I1**: Instance of type isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I1
- **InputNumber_I2**: Instance of type isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I2
- **AD_TO_AUDI_1**: Instance of type adapter::conversion::unidirectional::AD_TO_AUDI.
- **AD_TO_AUDI_2**: Instance of type adapter::conversion::unidirectional::AD_TO_AUDI.
- **AUDI_ADD_2**: Instance of type adapter::iec61131::arithmetic::AUDI_ADD_2.
- **Q_NumericValue**: Instance of type isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = OutputNumber_N1

### Connections and Interfaces

**Adapter Connections:**
- InputNumber_I1.IN -> AD_TO_AUDI_1.AD_IN
- InputNumber_I2.IN -> AD_TO_AUDI_2.AD_IN
- AD_TO_AUDI_1.AUDI_OUT -> AUDI_ADD_2.IN1
- AD_TO_AUDI_2.AUDI_OUT -> AUDI_ADD_2.IN2
- AUDI_ADD_2.OUT -> Q_NumericValue.u32NewValue

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_011b1_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
