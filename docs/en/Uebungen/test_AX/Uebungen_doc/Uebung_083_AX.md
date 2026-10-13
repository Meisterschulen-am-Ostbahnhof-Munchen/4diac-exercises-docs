# Exercise_083_AX: Example for AUDI_CTUD_UDINT (Adapter-Based)

![Uebung_083_AX_network](./Uebung_083_AX_network.svg)

* * * * * * * * * *

## Introduction

This article describes the logiBUS® exercise `Uebung_083_AX`. It is the adapter-based version of exercise 083 and demonstrates an up/down counter (`AUDI_CTUD_UDINT`) for unsigned 32-bit integers. The goal is to learn the adapter-based control of a counter and the display of the counter value on an ISOBUS terminal.

## Function Blocks (FBs) Used

- **DigitalInput_CLK_I1**: "Count up" button
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Single click on `I1` → `AUDI_CTUD_UDINT.CU`.
- **DigitalInput_CLK_I2**: "Count down" button
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Single click on `I2` → `AUDI_CTUD_UDINT.CD`.
- **DigitalInput_CLK_I3**: "Reset" button
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Single click on `I3` → `AUDI_CTUD_UDINT.R`.
- **DigitalInput_CLK_I4**: Input `I4`
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Instantiated in the network but not connected to any other block (in particular not to `LD`).
- **initval_AUDI**: Initial value for `PV`
    - **Type**: `adapter::types::unidirectional::AUDI::initval::initval_AUDI`
    - **Parameters**: `INIT_VAL = 5`
    - **Explanation**: Provides the UDINT value 5 as adapter `OUT` on initialization, which goes to `AUDI_CTUD_UDINT.PV`.
- **AUDI_CTUD_UDINT**: Adapter-based up/down counter
    - **Type**: `adapter::events::unidirectional::AUDI_CTUD_UDINT`
    - **Parameters**: none
    - **Explanation**: Events `CU` (up), `CD` (down), `R` (reset to 0) and `LD` (load CV with PV; not wired here). Adapter outputs: `QU` (TRUE if `CV >= PV`), `QD` (TRUE if `CV <= 0`) and `CV` (counter value). `PV` is an adapter input. The outputs are reported only on change.
- **DigitalOutput_Q1**: Digital output for `QU`
    - **Type**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Switches `Q1` according to `AUDI_CTUD_UDINT.QU`.
- **DigitalOutput_Q2**: Digital output for `QD`
    - **Type**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameters**: `QI = TRUE`, `Output = Output_Q2`
    - **Explanation**: Switches `Q2` according to `AUDI_CTUD_UDINT.QD`.
- **Q_NumericValue_AUDI**: Display on the terminal
    - **Type**: `isobus::UT::Q::Q_NumericValue_AUDI`
    - **Parameters**: `u16ObjId = OutputNumber_N1`
    - **Explanation**: Shows the counter value via the `AUDI` adapter (`u32NewValue`) in the VT number field `OutputNumber_N1`.

## Program Flow and Connections

1. `DigitalInput_CLK_I1.IND` → `AUDI_CTUD_UDINT.CU`: each click increments the counter by 1.
2. `DigitalInput_CLK_I2.IND` → `AUDI_CTUD_UDINT.CD`: each click decrements the counter by 1.
3. `DigitalInput_CLK_I3.IND` → `AUDI_CTUD_UDINT.R`: resets the counter to 0.
4. `initval_AUDI.OUT` → `AUDI_CTUD_UDINT.PV`: the preset value is 5.
5. `AUDI_CTUD_UDINT.QU` → `DigitalOutput_Q1.OUT` and `AUDI_CTUD_UDINT.QD` → `DigitalOutput_Q2.OUT`: status display of the counter.
6. `AUDI_CTUD_UDINT.CV` → `Q_NumericValue_AUDI.u32NewValue`: the counter value is displayed on the terminal.

**Behavior**: `Q1` is ON as soon as the counter value reaches or exceeds the preset value 5; `Q2` is ON while the counter value is 0.

## Summary

The exercise shows a fully adapter-based counter: events from buttons drive `AUDI_CTUD_UDINT`, the preset value is supplied via an `AUDI` adapter (`initval_AUDI`), and status outputs and counter value are passed to outputs and the terminal without additional data wiring.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
