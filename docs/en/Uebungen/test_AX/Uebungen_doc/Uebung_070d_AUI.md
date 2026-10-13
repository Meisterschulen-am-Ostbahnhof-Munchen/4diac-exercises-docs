# Exercise_070d_AUI: WBSD Output on UT with AR_Timeout_Zero (Adapter Version)

![Uebung_070d_AUI_network](./Uebung_070d_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise outputs the wheel-based speed (Wheel Based Speed and Distance, WBSD) of the TECU on the ISOBUS terminal: with PHYS scaling and the Fendt circuit `AR_Timeout_Zero` (adapter version, analogous to the Krauternter sub-app `TECU_WBSD.SUB`). On a communication failure, `0.0` is displayed instead of freezing the last value.

## Function Blocks (FBs) Used

- **IA_WBSD**: ISOBUS adapter for Wheel Based Speed and Distance (WBSD)
    - **Type**: [`isobus::tecu::IA_WBSD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_WBSD/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Provides the wheel-based speed as `AUI` adapter `SPEED` and the timeout status as `AX` adapter `TIMEOUT`.
- **Conv_WBSD**: Scaling of the fieldbus value
    - **Type**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED/)
    - **Parameters**: `SCALE = 0.0036`, `OFFSET = 0`
    - **Explanation**: Converts the `AUI` value (UINT from the fieldbus) into a scaled `AR` value (REAL) only if the signal is valid; `OFFSET` is added after scaling.
- **TZ_WBSD**: Fendt circuit (sub-app)
    - **Type**: `MyLib::sys::AR_Timeout_Zero`
    - **Parameters**: none
    - **Explanation**: Outputs `IN0` unchanged, unless `G` (here `IA_WBSD.TIMEOUT`, a real communication failure) is `TRUE`: then `0.0` is output instead of the otherwise frozen last value.
- **Q_NumericValue**: Display on the ISOBUS terminal
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = NumberVariable_TECU_Speed_WBSD_N`
    - **Explanation**: Shows the physical value (`rPhys`) in the object `NumberVariable_TECU_Speed_WBSD_N`.

## Program Flow and Connections

1. `IA_WBSD.SPEED` → `Conv_WBSD.IN` (AdapterConnection): The speed value from the TECU is scaled.
2. `Conv_WBSD.OUT` → `TZ_WBSD.IN0` (AdapterConnection): The scaled value is the live value of the Fendt circuit.
3. `IA_WBSD.TIMEOUT` → `TZ_WBSD.G` (AdapterConnection): The timeout status controls whether `0.0` is output.
4. `TZ_WBSD.OUT` → `Q_NumericValue.rPhys` (AdapterConnection): The result is shown on the terminal.

**Behavior**: As long as the TECU delivers the message, the terminal shows the value scaled with `0.0036`. If communication fails (`TIMEOUT`), it shows `0.0`.

## Summary

The exercise shows the chain TECU adapter → scaling → Fendt circuit → display. The reusable sub-app `AR_Timeout_Zero` (from `MyLib_AX`) encapsulates the selection between the live value and `0.0`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
