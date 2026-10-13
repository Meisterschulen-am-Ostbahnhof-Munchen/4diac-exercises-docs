# Exercise_074_AUI: Output RPTO to UT (Adapter Version) with Fendt Circuit

![Uebung_074_AUI_network](./Uebung_074_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise demonstrates the output of the speed of the rear power take-off (Rear PTO) to a User Terminal (UT) using adapters. It implements a so-called "Fendt circuit": on a timeout of the PTO signal, the value 0 is shown on the UT (comment in the network). This achieves safe behavior in case of sensor failure or communication faults.

The exercise is based on ISOBUS components (TECU interface) and shows how to use adapters for type conversion and signal selection.

## Function Blocks (FBs) Used

- **IA_RPTO**: ISOBUS adapter for the rear power take-off
    - **Type**: [`isobus::tecu::IA_RPTO`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_RPTO/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Interface to the rear PTO (ISO 11783-7, PGN 65091). The adapter `SPEED` delivers the speed as an AUI value, the adapter `TIMEOUT` reports the timeout status. After initialization, `INITO` is output.
- **initval_AUI**: substitute value 0
    - **Type**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameters**: `INIT_VAL = 0`
    - **Explanation**: Provides the constant value 0 as an AUI signal. It is displayed instead of the speed on a timeout.
- **AUI_AX_SEL_AUI**: selection block
    - **Type**: [`adapter::iec61131::selection::AUI_AX_SEL_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/selection/AUI_AX_SEL_AUI/)
    - **Parameters**: none
    - **Explanation**: Uses the AX signal `G` to select between the AUI values `IN0` (when `G` = FALSE) and `IN1` (when `G` = TRUE) and outputs the selected value at `OUT`. Here it implements the Fendt circuit.
- **CONV_AUI_AUDI**: conversion adapter
    - **Type**: [`adapter::conversion::unidirectional::AUI_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AUDI_AUI/AUI_TO_AUDI/)
    - **Parameters**: none
    - **Explanation**: Converts the AUI value (UINT) into the AUDI format (UDINT) expected by the display block.
- **Q_NumericValue_PTO**: VT display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = NumberVariable_Rear_PTO_output_shaft_speed`
    - **Explanation**: Receives the value via the socket `u32NewValue` and shows it on the User Terminal under the given object ID. It is initialized via the event `INIT`.

## Program Flow and Connections

1. `IA_RPTO.INITO` → `Q_NumericValue_PTO.INIT` (EventConnection): After the PTO adapter has been initialized, the display is initialized.
2. `IA_RPTO.SPEED` → `AUI_AX_SEL_AUI.IN0` (AdapterConnection): The measured speed is applied to the input `IN0` of the selector.
3. `initval_AUI.OUT` → `AUI_AX_SEL_AUI.IN1`: The value 0 is applied to the input `IN1`.
4. `IA_RPTO.TIMEOUT` → `AUI_AX_SEL_AUI.G`: The timeout status controls the selection. In normal operation (`TIMEOUT` = FALSE) the speed (`IN0`) is passed through, on a timeout (`TIMEOUT` = TRUE) the value 0 (`IN1`).
5. `AUI_AX_SEL_AUI.OUT` → `CONV_AUI_AUDI.AUI_IN`: The selected value is converted to the AUDI format.
6. `CONV_AUI_AUDI.AUDI_OUT` → `Q_NumericValue_PTO.u32NewValue`: The value is passed to the display and shown on the User Terminal - the actual speed in normal operation, a 0 on a timeout.

**Learning objectives**:

- Understanding the use of adapters to connect different data types (AUI, AUDI).
- Implementing a simple fallback logic (Fendt circuit) with a 2-channel selector.
- Working with ISOBUS TECU blocks and UT display blocks in 4diac.
- Recognizing and handling timeout situations in fieldbus communication.

**Setup note**: The object ID `NumberVariable_Rear_PTO_output_shaft_speed` must be defined in the ISOBUS object pool used.

## Summary

The exercise `Uebung_074_AUI` implements a safe display of the PTO speed on a User Terminal. By combining an adapter selector with a constant zero value in the error case, a simple but robust Fendt circuit is realized. It conveys basic concepts of adapter communication and event-based data preprocessing in 4diac.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
