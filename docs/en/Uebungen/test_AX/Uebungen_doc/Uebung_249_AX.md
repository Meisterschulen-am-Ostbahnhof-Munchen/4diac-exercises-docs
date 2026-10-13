# Uebung_249_AX: Asynchronous Object Attribute Query with I_GetAttribute and Attribute_IDA

![Uebung_249_AX_network](./Uebung_249_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise queries an attribute of a VT object asynchronously when a button (`Input_I1`) is pressed and displays it on `OutputNumber_N1`. The command is sent by `I_GetAttribute`, and the response arrives independently via `Attribute_IDA`: the two blocks are not connected by an event but refer to the same object and attribute through the same parameters (`u16ObjId = InputNumber_I1`, `u8AID = VALUE`). The received value is converted into a `REAL` adapter value via `AD_TO_AR_NUM` and output with `Q_NumericValue_PHYSA`.

## Function Blocks (FBs) Used

- **DigitalInput_I1**: Button with click detection
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I1` as event `IND`.
- **GetAttribute_I1**: Query command
    - **Type**: `isobus::UT::I::I_GetAttribute`
    - **Parameters**: `u16ObjId = InputNumber_I1`, `u8AID = VALUE`
    - **Explanation**: On `REQ`, requests the attribute `VALUE` of the object `InputNumber_I1`.
- **Attribute_IDA_I1**: Asynchronous reception of the attribute
    - **Type**: `isobus::UT::io::Attribute::Attribute_IDA`
    - **Parameters**: `QI = TRUE`, `u16ObjId = InputNumber_I1`, `u8AID = VALUE`
    - **Explanation**: Provides the received attribute value via the adapter `IN`.
- **AD_TO_AR_NUM**: Conversion `AD` to `AR`
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AR_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AD_TO_AR_NUM/)
    - **Parameters**: none
    - **Explanation**: Numerically converts the attribute value (`AD_IN`) to a `REAL` adapter value (`AR_OUT`).
- **OutputNumber_N1**: Numeric display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = OutputNumber_N1_N`
    - **Explanation**: Displays the value from `rPhys` on the object `OutputNumber_N1_N`.

## Program Flow and Connections

1. `DigitalInput_I1.IND` → `GetAttribute_I1.REQ` (EventConnection): A click on `I1` sends the attribute query.
2. `Attribute_IDA_I1.IN` → `AD_TO_AR_NUM.AD_IN` (AdapterConnection): The asynchronously received attribute value is converted.
3. `AD_TO_AR_NUM.AR_OUT` → `OutputNumber_N1.rPhys` (AdapterConnection): The value is displayed on `OutputNumber_N1`.

## Summary

Exercise 249_AX shows how an attribute of a VT object is queried: `I_GetAttribute` sends the command when the button is pressed, `Attribute_IDA` receives the response asynchronously, and `AD_TO_AR_NUM` brings the value into a `REAL` format for display via `Q_NumericValue_PHYSA`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
