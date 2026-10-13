# Exercise_213_AUDI: Standard IEC 61131-3 AUDI_FB_CTU (Adapter Version, Up Counter, UDINT) with Terminal Output

![Uebung_213_AUDI_network](./Uebung_213_AUDI_network.svg)

* * * * * * * * * *

## Introduction

This exercise shows an up counter (CTU) according to IEC 61131-3 as an adapter version for the data type UDINT. The counter is operated via two digital inputs (count and reset); the fixed preset value `5` comes from an `initval_AUDI` constant block. The count value is shown on the ISOBUS terminal, and reaching the preset value is output at `Q1`.

## Function Blocks (FBs) Used

- **Input_CU**: Count input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Provides the state of `I1` as adapter output `IN`.
- **Input_R**: Reset input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`
    - **Explanation**: Provides the state of `I2` as adapter output `IN`.
- **initval_AUDI**: Constant for the preset value
    - **Type**: [`adapter::types::unidirectional::AUDI::initval::initval_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UDINT/initval/initval_AUDI/)
    - **Parameters**: `INIT_VAL = 5`
    - **Explanation**: Provides the preset value `5` as `AUDI` adapter value to `AUDI_FB_CTU.PV`.
- **AUDI_FB_CTU**: Up counter (UDINT, adapter version)
    - **Type**: [`adapter::iec61131::counters::AUDI_FB_CTU`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/counters/AUDI_FB_CTU/)
    - **Parameters**: none
    - **Explanation**: Counts up via the adapter socket `CU`, `R` resets the counter, `PV` is the preset value. The adapter output `Q` is `TRUE` when the count value `CV` reaches or exceeds the preset value `PV`; `CV` provides the current count value.
- **Output_Q1**: Digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs the counter output `Q` at the physical output `Q1`.
- **Q_NumericValue_AUDI**: Display on the ISOBUS terminal
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = OutputNumber_N1`
    - **Explanation**: Receives the count value via the `AUDI` adapter `u32NewValue` and shows it in the object `OutputNumber_N1`.

## Program Flow and Connections

1. `Input_CU.IN` → `AUDI_FB_CTU.CU` (AdapterConnection): The state of `I1` is the count input.
2. `Input_R.IN` → `AUDI_FB_CTU.R` (AdapterConnection): The state of `I2` resets the counter.
3. `initval_AUDI.OUT` → `AUDI_FB_CTU.PV` (AdapterConnection): The preset value `5` is passed as preset.
4. `AUDI_FB_CTU.Q` → `Output_Q1.OUT` (AdapterConnection): The counter output is applied to `Q1`.
5. `AUDI_FB_CTU.CV` → `Q_NumericValue_AUDI.u32NewValue` (AdapterConnection): The count value is shown on the terminal.

**Behavior**: Each rising edge on `I1` increases the count value; `Q1` becomes `TRUE` as soon as the count value reaches or exceeds `5`. `I2` resets the counter. The count value appears in `OutputNumber_N1`. According to the network comment, an `AX_D_FF` can optionally be inserted here to reduce the number of events.

## Summary

The exercise shows the adapter-based connection of an IEC 61131-3 counter: inputs and outputs, preset value and display are wired purely via adapter connections, without additional event connections.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
