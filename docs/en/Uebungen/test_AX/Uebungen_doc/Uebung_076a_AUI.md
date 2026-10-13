# Exercise_076a_AUI: Output MSS with Compound Scaling to UT (Adapter Version) with Fendt Circuit + Timeout Zero

![Uebung_076a_AUI_network](./Uebung_076a_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise outputs the machine selected speed (MSS) of the TECU on a User Terminal (UT). The 16-bit raw value of the ISOBUS adapter is converted into a physical REAL value using compound scaling (separate factors for the upper and lower byte). In addition, the "Fendt circuit" with timeout zero is implemented: on a timeout the value 0.0 is shown on the UT (comment in the network: analogous to the Krauternter `TECU_MSS.SUB`). All connections run via adapters.

## Function Blocks (FBs) Used

- **IA_MSS**: ISOBUS adapter for the machine selected speed
    - **Type**: [`isobus::tecu::IA_MSS`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_MSS/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Provides the speed as an AUI value at the adapter `SPEED` and the timeout status at the adapter `TIMEOUT` (PGN 61474).
- **COMPOUND_SCALE**: compound scaling
    - **Type**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE/)
    - **Parameters**: `SCALE_HIGH = REAL#0.256`, `SCALE_LOW = REAL#0.001`, `OFFSET = DINT#0`
    - **Explanation**: Splits the 16-bit input value into upper and lower byte, scales them with `SCALE_HIGH` and `SCALE_LOW` respectively, adds them and adds the `OFFSET`. The result is available at the AR adapter `OUT`.
- **initval_AR**: substitute value 0.0
    - **Type**: [`adapter::types::unidirectional::AR::initval::initval_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/REAL/initval/initval_AR/)
    - **Parameters**: `INIT_VAL = REAL#0.0`
    - **Explanation**: Provides the constant value 0.0 as an AR signal; it is displayed instead of the speed on a timeout.
- **AR_AX_SEL_AR**: selection block
    - **Type**: [`adapter::iec61131::selection::AR_AX_SEL_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/selection/adapter/AR_AX_SEL_AR/)
    - **Parameters**: none
    - **Explanation**: Uses the AX signal `G` to select between the AR values `IN0` (`G` = FALSE) and `IN1` (`G` = TRUE) and outputs the selected value at `OUT`.
- **Q_NumericValue**: VT display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = NumberVariable_Wheel_based_machine_speed_N`
    - **Explanation**: Shows the physical REAL value from the adapter `rPhys` on the VT object given in the object pool entry `stObj`. According to the comment in the network, `NumberVariable_Wheel_based_machine_speed` is used as a substitute as long as `NumberVariable_Machine_selected_speed` has not yet been created in the object pool (TODO).

## Program Flow and Connections

1. `IA_MSS.SPEED` → `COMPOUND_SCALE.IN` (AdapterConnection): The raw speed value is scaled.
2. `COMPOUND_SCALE.OUT` → `AR_AX_SEL_AR.IN0`: The scaled value is the live value (`IN0`).
3. `initval_AR.OUT` → `AR_AX_SEL_AR.IN1`: The value 0.0 is applied to the input `IN1`.
4. `IA_MSS.TIMEOUT` → `AR_AX_SEL_AR.G`: The timeout status controls the selection. In normal operation (`TIMEOUT` = FALSE) the speed is passed through, on a timeout (`TIMEOUT` = TRUE) the value 0.0.
5. `AR_AX_SEL_AR.OUT` → `Q_NumericValue.rPhys`: The selected value is shown on the UT.

**Behavior**: In normal operation the UT shows the scaled machine selected speed, on a timeout of the TECU message 0.0.

## Summary

The exercise combines compound scaling of the 16-bit TECU raw value with the Fendt circuit: an adapter selector replaces the measured value by 0.0 on a timeout, so that the display does not stay at the last value on a communication failure.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
