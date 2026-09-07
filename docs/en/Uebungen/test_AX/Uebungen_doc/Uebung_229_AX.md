# Exercise_229_AX: Two Pushbuttons, One Last-Wins Latch with AX_RF_TRIG

![Uebung_229_AX_network](./Uebung_229_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise connects two momentary pushbuttons (`I1`, `I2`) to the same digital output `Q1`. Since both pushbuttons are momentary (no continuous signal), a set-reset latch (`AX_SR`) is required. The exercise deliberately implements a last-wins latch: not only pressing, but also releasing both pushbuttons affects the same latch—the last edge of the latch determines the state of `Q1`.


## Function Blocks (FBs) Used

- **DigitalInput_I1**, **DigitalInput_I2**: logiBUS digital inputs

- **Type**: `logiBUS::io::DI::logiBUS_IXA`

- **Parameters**: `QI = TRUE`, `Input = Input_I1`, and `Input_I2`

- **Explanation**: Connect the two physical push-button inputs to the adapter interface.

- **AX_RF_TRIG_1**, **AX_RF_TRIG_2**: Edge detection per push button

- **Type**: `adapter::events::unidirectional::AX_RF_TRIG`

- **Parameters**: None

- **Explanation**: Each physical input is monitored by its own instance. Each instance reports the rising edge (`ER`, button pressed) and the falling edge (`EF`, button released) as two separate events.

- **AX_SR**: shared set-reset interlock

- **Type**: `adapter::events::unidirectional::AX_SR`

- **Parameters**: none

- **Explanation**: Set-dominant interlock. Both `AX_RF_TRIG` instances feed the same `AX_SR`: both rising edges (`ER` from I1 and I2) run to `AX_SR.S`, and both falling edges (`EF` from I1 and I2) run to `AX_SR.R`. This is legal in 4diac because EventConnections—unlike DataConnections—allow multiple sources to point to a single target; it results in a simple OR operation without any additional components.

- **DigitalOutput_Q1**: logiBUS digital output

- **Type**: `logiBUS::io::DQ::logiBUS_QXA`

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`

- **Description**: Passes the state of `AX_SR.Q` to the physical output `Q1`.

### Sub-Blocks: None

This exercise does not use any further sub-blocks; all function blocks are located directly at the top level of the sub-app.



## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_RF_TRIG_1.QI` and `DigitalInput_I2.IN` → `AX_RF_TRIG_2.QI` (AdapterConnections): The physical button states are passed to the respective edge detection.

2. `AX_RF_TRIG_1.ER` → `AX_SR.S` and `AX_RF_TRIG_2.ER` → `AX_SR.S` (EventConnections): The rising edges of both buttons set the latch.

3. `AX_RF_TRIG_1.EF` → `AX_SR.R` and `AX_RF_TRIG_2.EF` → `AX_SR.R` (EventConnections): The falling edges of both buttons reset the latch.

4. `AX_SR.Q` → `DigitalOutput_Q1.OUT` (AdapterConnection): The latch state is assigned to the physical output `Q1`.

**Important — Last-Wins, not "holds after release"**: The output `Q1` does not follow the state of a specific button, but always the last occurring edge, regardless of which button is pressed. Pressing `I1` turns `Q1` ON. Releasing `I1` immediately turns `Q1` OFF again—even if `I2` is still being held down at that time. Similarly, the rising edge of `I2` sets `Q1` ON, and the falling edge of `I2` immediately turns `Q1` OFF again, regardless of the state of `I1`. Releasing either of the two buttons immediately turns `Q1` OFF, even if the other button is still being held—this is explicitly not a behavior where `Q1` remains ON as long as either button is held.

## Summary

Exercise 229 demonstrates how two independent event sources can be routed to the same set or reset input of a latch via simple EventConnections, without requiring an additional OR gate. The result is a latch deliberately designed as a "last-win" latch, whose state always follows the last occurring edge—a behavior distinct from classic hold logic. See `Uebung_230_AX.md` for the same function implemented with ASR adapter blocks instead of loose event wiring.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
