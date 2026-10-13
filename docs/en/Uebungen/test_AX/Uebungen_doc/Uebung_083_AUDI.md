# Uebung_083_AUDI: Example for AUDI_CTUD_UDINT (Adapter-Based)

![Uebung_083_AUDI_network](./Uebung_083_AUDI_network.svg)

* * * * * * * * * *

## Introduction

This exercise demonstrates the up/down counter `AUDI_CTUD_UDINT` based on adapters. Three buttons with single-click detection control the counter: `I1` counts up (`CU`), `I2` counts down (`CD`), `I3` resets (`R`). The preset value `PV` comes permanently from `initval_AUDI` (`INIT_VAL = 5`). The counter value `CV` is output at the display `OutputNumber_N1`, and the outputs `QU` and `QD` switch `Q1` and `Q2`. The block `DigitalInput_CLK_I4` (input `I4`) is present in the network but not wired.

## Function Blocks (FBs) Used

- **DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**, **DigitalInput_CLK_I3**, **DigitalInput_CLK_I4**: Buttons with click detection
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1` / `Input_I2` / `Input_I3` / `Input_I4`, each with `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Report a single click on the respective input as event `IND`.
- **AUDI_CTUD_UDINT**: Adapter-based up/down counter for `UDINT`
    - **Type**: [`adapter::events::unidirectional::AUDI_CTUD_UDINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UDINT/AUDI_CTUD_UDINT/)
    - **Parameters**: none
    - **Explanation**: Event inputs `CU` (up), `CD` (down), `R` (reset). `CV` is the current count value (adapter), `QU` is `TRUE` if `CV` is greater than or equal to `PV`, `QD` is `TRUE` if `CV` is less than or equal to 0.
- **initval_AUDI**: Preset value
    - **Type**: [`adapter::types::unidirectional::AUDI::initval::initval_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UDINT/initval/initval_AUDI/)
    - **Parameters**: `INIT_VAL = 5`
    - **Explanation**: Provides the value 5 as preset value `PV` to the counter.
- **DigitalOutput_Q1**, **DigitalOutput_Q2**: logiBUS digital outputs
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1` and `Output_Q2`, respectively
    - **Explanation**: `Q1` shows `QU`, `Q2` shows `QD`.
- **Q_NumericValue_AUDI**: numeric display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = OutputNumber_N1`
    - **Explanation**: Displays the counter value `CV` (`u32NewValue`) on `OutputNumber_N1`.

## Program Flow and Connections

1. `DigitalInput_CLK_I1.IND` → `AUDI_CTUD_UDINT.CU`, `DigitalInput_CLK_I2.IND` → `AUDI_CTUD_UDINT.CD` and `DigitalInput_CLK_I3.IND` → `AUDI_CTUD_UDINT.R` (EventConnections): A click on `I1` counts up, on `I2` down, and on `I3` the counter is reset.
2. `initval_AUDI.OUT` → `AUDI_CTUD_UDINT.PV` (AdapterConnection): The preset value 5.
3. `AUDI_CTUD_UDINT.QU` → `DigitalOutput_Q1.OUT` and `AUDI_CTUD_UDINT.QD` → `DigitalOutput_Q2.OUT` (AdapterConnections): `Q1` is on when the count value reaches or exceeds the preset value; `Q2` is on when the count value is 0 (or less).
4. `AUDI_CTUD_UDINT.CV` → `Q_NumericValue_AUDI.u32NewValue` (AdapterConnection): The counter value is displayed numerically.

## Summary

The exercise illustrates the use of the adapter-based up/down counter `AUDI_CTUD_UDINT`. Three buttons count up, count down and reset; the preset value 5 comes from `initval_AUDI`. `QU` and `QD` are routed to `Q1` and `Q2`, and the current counter value is displayed at `OutputNumber_N1`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
