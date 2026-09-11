# Uebung_015a_AX: Object Pointer umschalten -- 3-fach

This article describes the 4diac IDE sub-application Uebung_015a_AX (Object Pointer umschalten -- 3-fach).

----

![Uebung_015a_AX_network](./Uebung_015a_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Object Pointer umschalten -- 3-fach**

-----

## Description and Components

The exercise consists of the sub-application Uebung_015a_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **SoftKey_UP_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_UP_F2**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_RELEASED
- **F_UINT_TO_UDINT**: Instance of type adapter::types::unidirectional::AUDI::initval::initval_AUDI.
  - Parameter INIT_VAL = Button_A1
- **Q_NumericValue_AUDI**: Instance of type isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = ObjectPointer_P1
- **SoftKey_UP_F3**: Instance of type isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_RELEASED
- **F_UINT_TO_UDINT_1**: Instance of type adapter::types::unidirectional::AUDI::initval::initval_AUDI.
  - Parameter INIT_VAL = Button_A2
- **initval_AUDI**: Instance of type adapter::types::unidirectional::AUDI::initval::initval_AUDI.
  - Parameter INIT_VAL = ID_NULL
- **AUDI_AUI_MUX_3**: Instance of type adapter::selection::unidirectional::AUDI_AUI_MUX_3.
- **AUI_MUX_3**: Instance of type adapter::events::unidirectional::AUI_MUX_3.

### Connections and Interfaces

**Adapter Connections:**

- AUDI_AUI_MUX_3.OUT -> Q_NumericValue_AUDI.u32NewValue
- F_UINT_TO_UDINT_1.OUT -> AUDI_AUI_MUX_3.IN3
- F_UINT_TO_UDINT.OUT -> AUDI_AUI_MUX_3.IN2
- initval_AUDI.OUT -> AUDI_AUI_MUX_3.IN1
- AUI_MUX_3.K -> AUDI_AUI_MUX_3.K

**Event Connections:**

- SoftKey_UP_F1.IND -> AUI_MUX_3.EI1
- SoftKey_UP_F2.IND -> AUI_MUX_3.EI2
- SoftKey_UP_F3.IND -> AUI_MUX_3.EI3

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_015a_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
