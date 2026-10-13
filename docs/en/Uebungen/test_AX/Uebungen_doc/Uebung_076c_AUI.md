# Uebung_076c_AUI: Output MSS with Compound Scaling + AR_Timeout_Zero on the UT (Adapter Version)

![Uebung_076c_AUI_network](./Uebung_076c_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise outputs the MSS speed of the TECU on the Universal Terminal (UT). The raw value is converted into a physical `REAL` value with a compound scaling (`SCALE_HIGH = 0.256`, `SCALE_LOW = 0.001`, `OFFSET = 0`) and passed through the sub-app `AR_Timeout_Zero` (Fendt circuit): in case of a communication failure (`IA_MSS.TIMEOUT`), `0.0` is output. All connections are adapters (analogous to `TECU_MSS.SUB` in the Krauternter project).

## Function Blocks (FBs) Used

- **IA_MSS**: TECU input MSS
    - **Type**: [`isobus::tecu::IA_MSS`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_MSS/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Provides `SPEED` (raw value) and `TIMEOUT` (communication failure) as adapters.
- **COMPOUND_SCALE**: Compound scaling
    - **Type**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE/)
    - **Parameters**: `SCALE_HIGH = REAL#0.256`, `SCALE_LOW = REAL#0.001`, `OFFSET = DINT#0`
    - **Explanation**: Converts the `UINT` raw value (`IN`) with the two scaling factors `SCALE_HIGH` and `SCALE_LOW` into a `REAL` adapter value (`OUT`).
- **TZ_MSS**: Fendt circuit (sub-app)
    - **Type**: `MyLib::sys::AR_Timeout_Zero` (SubApp instance)
    - **Parameters**: none
    - **Explanation**: `OUT` delivers `IN0` unchanged unless `G` is `TRUE` (timeout), in which case it delivers `0.0`.
- **Q_NumericValue**: numeric display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = NumberVariable_TECU_Speed_MSS_N`
    - **Explanation**: Displays the physical value from `rPhys` on the UT object `NumberVariable_TECU_Speed_MSS_N`.

## Program Flow and Connections

1. `IA_MSS.SPEED` → `COMPOUND_SCALE.IN` (AdapterConnection): The raw value is converted with compound scaling.
2. `COMPOUND_SCALE.OUT` → `TZ_MSS.IN0` (AdapterConnection): The scaled value is the live value of the Fendt circuit.
3. `IA_MSS.TIMEOUT` → `TZ_MSS.G` (AdapterConnection): A timeout switches the output to `0.0`.
4. `TZ_MSS.OUT` → `Q_NumericValue.rPhys` (AdapterConnection): The result is displayed on the UT.

## Summary

Exercise 076c_AUI has the same structure as `Uebung_072d_AUI` but uses the block `AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE` with two scaling factors instead of the simple scaling. The protection against communication failure is again provided by `AR_Timeout_Zero`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
