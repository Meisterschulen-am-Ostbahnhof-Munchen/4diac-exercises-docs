# Uebung_011f_ROUND_AR_AX: Rounding a Physical Numeric Value with OSCAT ROUND_AR

![Uebung_011f_ROUND_AR_AX_network](./Uebung_011f_ROUND_AR_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise corresponds to `Uebung_011f_AX`, but the OSCAT block `ROUND_AR`, which rounds the value to N decimal places, is inserted between input and output. The physical numeric value of the input `InputNumber_I3_N` is passed as a `REAL` adapter (`rPhys`) through `ROUND_AR` and displayed at the output `OutputNumber_N3_N`.

## Function Blocks (FBs) Used

- **NumericValue_PHYS**: Input of a physical numeric value
    - **Type**: [`isobus::UT::io::NumericValue::NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_PHYSA/)
    - **Parameters**: `QI = TRUE`, `stObj = InputNumber_I3_N`
    - **Explanation**: Provides the entered numeric value as a physical value via the adapter `rPhys`.
- **ROUND_AR**: Rounds to N decimal places
    - **Type**: `OSCAT_adapter::Mathematical::ROUND_AR`
    - **Parameters**: `N = 1`
    - **Explanation**: Rounds the value from `AR_IN` to `N` decimal places (here one) and outputs it via `AR_OUT`.
- **Q_NumericValue_PHYS**: Output of a physical numeric value
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = OutputNumber_N3_N`
    - **Explanation**: Displays the value received via `rPhys` at `OutputNumber_N3_N`.

## Program Flow and Connections

1. `NumericValue_PHYS.rPhys` → `ROUND_AR.AR_IN` (AdapterConnection): The physical input value goes into the rounding block.
2. `ROUND_AR.AR_OUT` → `Q_NumericValue_PHYS.rPhys` (AdapterConnection): The rounded value is passed on to the output.

**Example** (from the comment in the network): input I3 = -500.00 → `rPhys` = -500.0 → `ROUND_AR` (`N = 1`) → -500.0 → `Q_NumericValue_PHYS` (N3) → N3 shows -500.00.

## Summary

The exercise shows how an OSCAT block (`ROUND_AR`) can be looped into the adapter path of a physical numeric value without changing the input and output blocks. Compared with `Uebung_011f_AX`, the value is thereby rounded to the configured number of decimal places.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
