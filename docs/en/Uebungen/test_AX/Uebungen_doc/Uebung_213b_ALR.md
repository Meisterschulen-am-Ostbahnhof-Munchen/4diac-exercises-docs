# Uebung_213b_ALR: Standard IEC 61131-3 AUDI_FB_CTU (Adapter Version, Up Counter, UDINT) with Terminal Output (PHYSA_LREAL)

![Uebung_213b_ALR_network](./Uebung_213b_ALR_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements an up counter (CTU) according to IEC 61131-3 with the counting range `UDINT` as an adapter version. Button `I1` counts up (`CU`), `I2` resets (`R`). The preset value `PV` is permanently set to 5 via `initval_AUDI`, so the output `Q` becomes active when 5 is reached and switches `Q1`. The counter value `CV` is converted into an `LREAL` value and output at the terminal object `OutputNumber_N3_N`.

## Function Blocks (FBs) Used

- **Input_CU**: Count pulses
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Provides the state of `I1` as adapter `IN`; goes to `AUDI_FB_CTU.CU`.
- **Input_R**: Reset
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`
    - **Explanation**: Provides the state of `I2` as adapter `IN`; goes to `AUDI_FB_CTU.R`.
- **initval_AUDI**: Preset value
    - **Type**: [`adapter::types::unidirectional::AUDI::initval::initval_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UDINT/initval/initval_AUDI/)
    - **Parameters**: `INIT_VAL = 5`
    - **Explanation**: Provides the value 5 as preset value `PV` to the counter.
- **AUDI_FB_CTU**: Up counter
    - **Type**: [`adapter::iec61131::counters::AUDI_FB_CTU`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/counters/AUDI_FB_CTU/)
    - **Parameters**: none
    - **Explanation**: Count input `CU`, reset input `R`, preset value `PV`; outputs `Q` (active when `CV` ≥ `PV`) and `CV` (current count value).
- **Output_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs `AUDI_FB_CTU.Q` at the physical output `Q1`.
- **AUDI_TO_ALR**: Conversion `AUDI` to `ALR`
    - **Type**: [`adapter::conversion::unidirectional::AUDI_TO_ALR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/ALR_AUDI/AUDI_TO_ALR/)
    - **Parameters**: none
    - **Explanation**: Converts the counter value (`AUDI_IN`) into an `LREAL` adapter value (`ALR_OUT`). According to the comment in the network, negative values are possible here.
- **Q_NumericValue_PHYSA_LREAL**: Terminal output
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA_LREAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA_LREAL/)
    - **Parameters**: `stObj = OutputNumber_N3_N`
    - **Explanation**: Displays the `LREAL` value from `lrPhys` on the terminal object `OutputNumber_N3_N`.

## Program Flow and Connections

1. `Input_CU.IN` → `AUDI_FB_CTU.CU` and `Input_R.IN` → `AUDI_FB_CTU.R` (AdapterConnections): `I1` counts, `I2` resets.
2. `initval_AUDI.OUT` → `AUDI_FB_CTU.PV` (AdapterConnection): Preset value 5.
3. `AUDI_FB_CTU.Q` → `Output_Q1.OUT` (AdapterConnection): `Q1` is active when the counter value reaches or exceeds the preset value.
4. `AUDI_FB_CTU.CV` → `AUDI_TO_ALR.AUDI_IN` and `AUDI_TO_ALR.ALR_OUT` → `Q_NumericValue_PHYSA_LREAL.lrPhys` (AdapterConnections): The counter value is displayed as `LREAL` on the terminal.

**Note from the network**: An `AX_D_FF` can be inserted here if necessary so that the events are reduced.

## Summary

Exercise 213b_ALR shows an adapter-based IEC up counter with a fixed preset value of 5, whose counter value is output on a terminal via `AUDI_TO_ALR`. All connections are adapter connections.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
