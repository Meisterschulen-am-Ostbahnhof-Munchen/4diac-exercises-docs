# Uebung_223b_ALR: Standard IEC 61131-3 AUDI_FB_CTUD (Adapter Version, Up/Down Counter, UDINT) with Terminal Output (PHYSA_LREAL)

![Uebung_223b_ALR_network](./Uebung_223b_ALR_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements an up/down counter (CTUD) according to IEC 61131-3 with the counting range `UDINT` as an adapter version. Four buttons control the counter: `I1` counts up (`CU`), `I2` counts down (`CD`), `I3` resets (`R`), `I4` triggers loading (`LD`). The preset value `PV` is permanently set to 5 via `initval_AUDI`. The outputs `QU` and `QD` switch `Q1` and `Q2`; the counter value `CV` is converted into an `LREAL` value and output at the terminal object `OutputNumber_N3_N`.

## Function Blocks (FBs) Used

- **Input_CU**, **Input_CD**, **Input_R**, **Input_LD**: Digital inputs
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1` (`Input_CU`), `Input_I2` (`Input_CD`), `Input_I3` (`Input_R`), `Input_I4` (`Input_LD`)
    - **Explanation**: Provide the state of the respective input as adapter `IN`; they go to `CU`, `CD`, `R` and `LD` of the counter, respectively.
- **initval_AUDI**: Preset value
    - **Type**: [`adapter::types::unidirectional::AUDI::initval::initval_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UDINT/initval/initval_AUDI/)
    - **Parameters**: `INIT_VAL = 5`
    - **Explanation**: Provides the value 5 as preset value `PV` to the counter.
- **AUDI_FB_CTUD**: Up/down counter
    - **Type**: [`adapter::iec61131::counters::AUDI_FB_CTUD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/counters/AUDI_FB_CTUD/)
    - **Parameters**: none
    - **Explanation**: Inputs `CU`, `CD`, `R`, `LD` and `PV`; outputs `QU`, `QD` and `CV` (current counter value), each as an adapter.
- **Output_QU**, **Output_QD**: logiBUS digital outputs
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1` (`Output_QU`), `Output_Q2` (`Output_QD`)
    - **Explanation**: Output `AUDI_FB_CTUD.QU` to `Q1` and `AUDI_FB_CTUD.QD` to `Q2`.
- **AUDI_TO_ALR**: Conversion `AUDI` to `ALR`
    - **Type**: [`adapter::conversion::unidirectional::AUDI_TO_ALR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/ALR_AUDI/AUDI_TO_ALR/)
    - **Parameters**: none
    - **Explanation**: Converts the counter value (`AUDI_IN`) into an `LREAL` adapter value (`ALR_OUT`). According to the comment in the network, negative values are possible here.
- **Q_NumericValue_PHYSA_LREAL**: Terminal output
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA_LREAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA_LREAL/)
    - **Parameters**: `stObj = OutputNumber_N3_N`
    - **Explanation**: Displays the `LREAL` value from `lrPhys` on the terminal object `OutputNumber_N3_N`.

## Program Flow and Connections

1. `Input_CU.IN` → `AUDI_FB_CTUD.CU`, `Input_CD.IN` → `AUDI_FB_CTUD.CD`, `Input_R.IN` → `AUDI_FB_CTUD.R` and `Input_LD.IN` → `AUDI_FB_CTUD.LD` (AdapterConnections): The four buttons control the counter.
2. `initval_AUDI.OUT` → `AUDI_FB_CTUD.PV` (AdapterConnection): Preset value 5.
3. `AUDI_FB_CTUD.QU` → `Output_QU.OUT` and `AUDI_FB_CTUD.QD` → `Output_QD.OUT` (AdapterConnections): The counter outputs switch `Q1` and `Q2`.
4. `AUDI_FB_CTUD.CV` → `AUDI_TO_ALR.AUDI_IN` and `AUDI_TO_ALR.ALR_OUT` → `Q_NumericValue_PHYSA_LREAL.lrPhys` (AdapterConnections): The counter value is displayed as `LREAL` on the terminal.

**Note from the network**: One `AX_D_FF` each can be inserted here if necessary so that the events are reduced.

## Summary

Exercise 223b_ALR shows an adapter-based IEC up/down counter with a fixed preset value of 5 and terminal output of the counter value via `AUDI_TO_ALR`. All connections are adapter connections.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
