# Exercise_218b_ALR: Standard IEC 61131-3 AUDI_FB_CTD (Adapter Version, Down Counter, UDINT) with Terminal Output (PHYSA_LREAL)

![Uebung_218b_ALR_network](./Uebung_218b_ALR_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements a **down counter (CTD)** according to IEC 61131-3 with an `AUDI_FB_CTD` (adapter version, data type UDINT). The counter is controlled via two digital inputs (`I1` = count down, `I2` = load); the preset value 10 comes from an `initval_AUDI`. The current counter value is converted into an LREAL value via `AUDI_TO_ALR` and output on the terminal with `Q_NumericValue_PHYSA_LREAL`. In addition, a digital output (`Output_Q1`) shows the counter output `Q`.

Two comments in the network point out: "negative values are possible here!" (for the LREAL output) and "if necessary, install an AX_D_FF here to reduce the events." (at the output `Q`).

## Function Blocks (FBs) Used

- **Input_CD**: input for the count-down pulse
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Provides the state of `I1` as an AX adapter signal; it feeds `AUDI_FB_CTD.CD`.
- **Input_LD**: input for loading
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`
    - **Explanation**: Provides the state of `I2` as an AX adapter signal; it feeds `AUDI_FB_CTD.LD`.
- **initval_AUDI**: preset value
    - **Type**: [`adapter::types::unidirectional::AUDI::initval::initval_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UDINT/initval/initval_AUDI/)
    - **Parameters**: `INIT_VAL = 10`
    - **Explanation**: Provides the constant value 10 as an AUDI signal.
- **AUDI_FB_CTD**: down counter
    - **Type**: [`adapter::iec61131::counters::AUDI_FB_CTD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/counters/AUDI_FB_CTD/)
    - **Parameters**: none
    - **Explanation**: Counts down via the adapter `CD`, loads the preset value `PV` (UDINT) via `LD`, and outputs the current counter value at the adapter `CV` and the counter output at the adapter `Q` (according to the block reference: TRUE when the counter value has reached 0).
- **Output_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs the counter output `Q` at the physical output `Q1`.
- **AUDI_TO_ALR**: conversion adapter
    - **Type**: [`adapter::conversion::unidirectional::AUDI_TO_ALR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/ALR_AUDI/AUDI_TO_ALR/)
    - **Parameters**: none
    - **Explanation**: Converts the counter value (UDINT, adapter `AUDI_IN`) into an LREAL value (adapter `ALR_OUT`).
- **Q_NumericValue_PHYSA_LREAL**: VT display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA_LREAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA_LREAL/)
    - **Parameters**: `stObj = OutputNumber_N3_N`
    - **Explanation**: Shows the physical LREAL value from the adapter `lrPhys` on the VT object given in the object pool entry `stObj`.

## Program Flow and Connections

1. `Input_CD.IN` → `AUDI_FB_CTD.CD` (AdapterConnection): The state of `I1` is the count input.
2. `Input_LD.IN` → `AUDI_FB_CTD.LD`: The state of `I2` triggers loading.
3. `initval_AUDI.OUT` → `AUDI_FB_CTD.PV`: The preset value 10 is given to the counter.
4. `AUDI_FB_CTD.Q` → `Output_Q1.OUT`: The counter output is applied to `Q1`.
5. `AUDI_FB_CTD.CV` → `AUDI_TO_ALR.AUDI_IN`: The counter value is converted to LREAL.
6. `AUDI_TO_ALR.ALR_OUT` → `Q_NumericValue_PHYSA_LREAL.lrPhys`: The value is output on the terminal.

The network contains no event connections; all connections are adapter connections.

**Learning objectives:**

- Structure and parameterization of an adapter-based CTD function block
- Integration of digital inputs/outputs via logiBUS adapters
- Data conversion (UDINT → LREAL) for output purposes
- Recognizing problems at high event rates and approaches to solve them (`AX_D_FF`)

## Summary

The exercise `Uebung_218b_ALR` teaches the use of an IEC 61131-3 down counter in the 4diac IDE. The counter is controlled via two digital inputs (count and load), its preset value comes from an `initval_AUDI`, and it outputs its status and the current value. The special feature is the conversion of the UDINT counter value into an LREAL terminal output.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
