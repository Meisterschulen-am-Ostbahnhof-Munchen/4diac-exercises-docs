# Exercise_078_AUI: Output FHS Position/Force/Draft to UT with AR_Timeout_Zero (Adapter Version)

![Uebung_078_AUI_network](./Uebung_078_AUI_network.svg)

* * * * * * * * * *

## Introduction

This exercise outputs the three measured values of the Front Hitch Status (FHS) - position, force and draft - on a User Terminal (UT). Each raw value is converted into a REAL value and passed through an `AR_Timeout_Zero` block, which delivers the value 0.0 on a timeout of the FHS message instead of freezing the last value. The common timeout signal is distributed to three targets for this purpose. All connections run via adapters.

## Function Blocks (FBs) Used

- **FHS_Inst**: ISOBUS adapter for the front hitch status
    - **Type**: [`isobus::tecu::IA_FHS`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_FHS/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Provides `POSITION` and `FORCE` as AUS adapters, `DRAFT` as an AUI adapter and the timeout status `TIMEOUT` as an AX adapter (PGN 65094).
- **Conv_Position**, **Conv_Force**: conversion AUS → AR
    - **Type**: `MyLib::sys::AUS_FIELDBUS_USINT_TO_AR` (SubApp instance, see [AUS_FIELDBUS_USINT_TO_AR](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/AUS_FIELDBUS_USINT_TO_AR/))
    - **Parameters**: none
    - **Explanation**: Converts the TECU raw value (USINT) unscaled (`SCALE` = 1.0, `OFFSET` = 0) into an AR value. On `NOT_AVAILABLE` or `ERROR_INDI` it delivers 0.0.
- **Conv_Draft**: conversion AUI → AR
    - **Type**: `MyLib::sys::AUI_FIELDBUS_UINT_TO_AR` (SubApp instance)
    - **Parameters**: none
    - **Explanation**: As above, but for the UINT raw value of the AUI adapter `DRAFT`.
- **TZ_Position**, **TZ_Force**, **TZ_Draft**: timeout zero
    - **Type**: `MyLib::sys::AR_Timeout_Zero` (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Fendt circuit: the adapter `OUT` delivers the live value `IN0` unchanged, unless `G` (usually `IA_*.TIMEOUT`) is TRUE - then 0.0 instead of the otherwise frozen last value. Internally the block consists of an `initval_AR` with `INIT_VAL = 0.0` and an `AR_AX_SEL_AR`.
- **AX_SPLIT_3_Timeout**: distributor of the timeout signal
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_3/)
    - **Parameters**: none
    - **Explanation**: Distributes the timeout signal from `FHS_Inst.TIMEOUT` to the three timeout-zero blocks.
- **Q_NumericValue_FHS_Position**, **Q_NumericValue_FHS_Force**, **Q_NumericValue_FHS_Draft**: VT displays
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = NumberVariable_TECU_FHS_Position_N`, `NumberVariable_TECU_FHS_Force_N` and `NumberVariable_TECU_FHS_Draft_N` respectively
    - **Explanation**: Show the physical REAL value from the adapter `rPhys` on the VT object given in the object pool entry `stObj`.

## Program Flow and Connections

1. Position: `FHS_Inst.POSITION` → `Conv_Position.IN` → `TZ_Position.IN0` (`Conv_Position.OUT`) → `Q_NumericValue_FHS_Position.rPhys` (`TZ_Position.OUT`).
2. Force: `FHS_Inst.FORCE` → `Conv_Force.IN` → `TZ_Force.IN0` (`Conv_Force.OUT`) → `Q_NumericValue_FHS_Force.rPhys` (`TZ_Force.OUT`).
3. Draft: `FHS_Inst.DRAFT` → `Conv_Draft.IN` → `TZ_Draft.IN0` (`Conv_Draft.OUT`) → `Q_NumericValue_FHS_Draft.rPhys` (`TZ_Draft.OUT`).
4. `FHS_Inst.TIMEOUT` → `AX_SPLIT_3_Timeout.IN`: The timeout signal is distributed.
5. `AX_SPLIT_3_Timeout.OUT1` → `TZ_Position.G`, `AX_SPLIT_3_Timeout.OUT2` → `TZ_Force.G`, `AX_SPLIT_3_Timeout.OUT3` → `TZ_Draft.G`: On a timeout all three timeout-zero blocks deliver 0.0.

**Behavior**: In normal operation the UT shows the three converted FHS values; on a timeout of the FHS message all three fields show 0.0.

## Summary

The exercise shows how the Fendt circuit can be applied to several measured values at once with the reusable block `AR_Timeout_Zero`: a single timeout signal is distributed to three channels via `AX_SPLIT_3`, so that all displays fall to 0.0 on a communication failure.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
