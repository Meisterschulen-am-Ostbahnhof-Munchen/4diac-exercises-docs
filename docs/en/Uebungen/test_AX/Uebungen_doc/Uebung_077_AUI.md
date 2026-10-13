# Exercise_077_AUI: Output RHS Position/Force/Draft on UT with AR_Timeout_Zero (Adapter Version)

![Uebung_077_AUI_network](./Uebung_077_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise outputs the values of the ISOBUS TECU Rear Hitch Status (**RHS**) on a Universal Terminal (UT): **Position**, **Force** (nominal lower link force) and **Draft**. Each raw value is converted into a REAL value (unscaled; NOT_AVAILABLE/ERROR_INDICATOR are filtered to `0.0`), protected against communication failure by its own `AR_Timeout_Zero` instance and displayed via `Q_NumericValue_PHYSA`. The exercise is analogous to `TECU_RHS.SUB` of the Krauternter software.

## Function Blocks (FBs) Used

- **RHS_Inst**: ISOBUS adapter for the rear hitch status
    - **Type**: `isobus::tecu::IA_RHS`
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Provides the adapters `POSITION` and `FORCE` (each AUS, USINT raw value), `DRAFT` (AUI, UINT raw value) and `TIMEOUT` (AX, TRUE on communication failure).
- **AX_SPLIT_3_Timeout**: Distributor for the timeout status
    - **Type**: `adapter::events::unidirectional::AX_SPLIT_3`
    - **Parameters**: none
    - **Explanation**: Distributes `RHS_Inst.TIMEOUT` to `OUT1` (→ `TZ_Position.G`), `OUT2` (→ `TZ_Force.G`) and `OUT3` (→ `TZ_Draft.G`).
- **Conv_Position**, **Conv_Force**: Conversion USINT → REAL
    - **Type**: `MyLib::sys::AUS_FIELDBUS_USINT_TO_AR` (SubApp instances)
    - **Parameters**: none
    - **Explanation**: Convert the TECU raw value (`IN`, AUS) into an AR value (`OUT`). NOT_AVAILABLE/ERROR_INDICATOR are filtered (output `0.0`), otherwise the value remains unscaled (`SCALE = 1.0`, `OFFSET = 0` fixed).
- **Conv_Draft**: Conversion UINT → REAL
    - **Type**: `MyLib::sys::AUI_FIELDBUS_UINT_TO_AR` (SubApp instance)
    - **Parameters**: none
    - **Explanation**: As above, but for the UINT raw value (`IN`, AUI).
- **TZ_Position**, **TZ_Force**, **TZ_Draft**: Timeout zeroing (SubApp instances)
    - **Type**: `MyLib::sys::AR_Timeout_Zero`
    - **Parameters**: none
    - **Explanation**: Pass `IN0` unchanged to `OUT`, unless `G` is TRUE (timeout) - then `0.0`.
- **Q_NumericValue_RHS_Position**, **Q_NumericValue_RHS_Force**, **Q_NumericValue_RHS_Draft**: Output to VT number fields
    - **Type**: `isobus::UT::Q::Q_NumericValue_PHYSA`
    - **Parameters**: `stObj = NumberVariable_TECU_RHS_Position_N` / `NumberVariable_TECU_RHS_Force_N` / `NumberVariable_TECU_RHS_Draft_N`
    - **Explanation**: Write the physical value (`rPhys`) into the corresponding VT object.

## Program Flow and Connections

1. Position: `RHS_Inst.POSITION` → `Conv_Position.IN` → `TZ_Position.IN0`; `AX_SPLIT_3_Timeout.OUT1` → `TZ_Position.G`; `TZ_Position.OUT` → `Q_NumericValue_RHS_Position.rPhys`.
2. Force: `RHS_Inst.FORCE` → `Conv_Force.IN` → `TZ_Force.IN0`; `AX_SPLIT_3_Timeout.OUT2` → `TZ_Force.G`; `TZ_Force.OUT` → `Q_NumericValue_RHS_Force.rPhys`.
3. Draft: `RHS_Inst.DRAFT` → `Conv_Draft.IN` → `TZ_Draft.IN0`; `AX_SPLIT_3_Timeout.OUT3` → `TZ_Draft.G`; `TZ_Draft.OUT` → `Q_NumericValue_RHS_Draft.rPhys`.
4. `RHS_Inst.TIMEOUT` → `AX_SPLIT_3_Timeout.IN`: one timeout status controls all three zeroings.

**Behavior**: As long as data arrives, the UT shows position, force and draft of the rear hitch. On a communication failure (`TIMEOUT` = TRUE), all three fields show `0.0`.

## Summary

The exercise transfers the pattern from `Uebung_073d_AUI` and `Uebung_075a_AUI` to three values with different raw data types (USINT and UINT): a matching converter SubApp, an `AR_Timeout_Zero` instance and a `Q_NumericValue_PHYSA` each, with a shared `AX_SPLIT_3` distributing the timeout status.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
