# Exercise_073d_AUI: Output VDS on UT with AR_Timeout_Zero (Adapter Version)

![Uebung_073d_AUI_network](./Uebung_073d_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise outputs the navigation-based vehicle speed (**VDS**) of the TECU on a Universal Terminal (UT). The raw value is received via the ISOBUS adapter `IA_VDS`, scaled with `0.00390625` (= 1/256) and written to the VT number field as a physical value (`PHYSA`). In addition, the "Fendt circuit" `AR_Timeout_Zero` is used: on a real communication failure (timeout), `0.0` is displayed instead of the otherwise simply frozen last value. The exercise is analogous to `TECU_VDS.SUB` of the Krauternter software and uses the adapter technique (*AUI*).

## Function Blocks (FBs) Used

- **IA_VDS**: ISOBUS adapter for Vehicle Direction/Speed
    - **Type**: `isobus::tecu::IA_VDS`
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Provides, among other things, the navigation speed as UINT adapter `NAV_SPEED` and the adapter `TIMEOUT` (AX, TRUE on communication failure). `BEARING` and `ALTITUDE` remain unused in this exercise.
- **Conv_VDS**: Scaling of the raw value
    - **Type**: `logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`
    - **Parameters**: `SCALE = 0.00390625`, `OFFSET = 0`
    - **Explanation**: Converts the UINT raw value (`IN`, AUI) into a scaled REAL value (`OUT`, AR); there is also an adapter `VALID`, which is not used here.
- **TZ_VDS**: Timeout zeroing (SubApp instance)
    - **Type**: `MyLib::sys::AR_Timeout_Zero`
    - **Parameters**: none
    - **Explanation**: Passes `IN0` unchanged to `OUT`, unless `G` is TRUE (timeout) - then `0.0` is output. Internally: `initval_AR` (`INIT_VAL = 0.0`) and the selector `AR_AX_SEL_AR`.
- **Q_NumericValue**: Output to the VT number field
    - **Type**: `isobus::UT::Q::Q_NumericValue_PHYSA`
    - **Parameters**: `stObj = NumberVariable_TECU_Speed_VDS_N`
    - **Explanation**: Writes the physical REAL value (`rPhys`) into the corresponding object of the object pool, taking scale, offset and decimals from `stObj` into account.

## Program Flow and Connections

1. `IA_VDS.NAV_SPEED` → `Conv_VDS.IN`: the raw value is fed to the scaling.
2. `Conv_VDS.OUT` → `TZ_VDS.IN0`: the scaled value is the live value of the timeout zeroing.
3. `IA_VDS.TIMEOUT` → `TZ_VDS.G`: the communication status decides between the live value and `0.0`.
4. `TZ_VDS.OUT` → `Q_NumericValue.rPhys`: the result is displayed on the UT.

**Behavior**: As long as data arrives from the TECU, the UT shows the scaled vehicle speed. If communication fails (`TIMEOUT` = TRUE), the display jumps to `0.0`.

## Summary

The exercise shows the complete signal chain of a TECU measurement value in adapter style: reception (`IA_VDS`), scaling (`AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`), protection against communication failure (`AR_Timeout_Zero`) and output on the terminal (`Q_NumericValue_PHYSA`).

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
