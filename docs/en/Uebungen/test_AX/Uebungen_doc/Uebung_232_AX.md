# Exercise_232_AX: Two ASRT Sources (Push Button + True Toggle Button) to One ASRT Latch

![Uebung_232_AX_network](./Uebung_232_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is a direct sibling to `Uebung_229_AX.md`/`Uebung_230_AX.md`, but consistently uses `ASRT` (Set/Reset/Toggle) instead of `ASR` (Set/Reset only): two independent sources each provide a part of the SET/RESET/TOGGLE semantics, and `ASRT_MERGE_2` combines both into a single `ASRT_AX_T_FF_SR` latch. This exercise demonstrates how a classic last-wins button and a true toggle button can be used independently to control the same output.

## Function Blocks (FBs) Used

- **DigitalInput_I1**: logiBUS digital input for the classic button

- **Type**: `logiBUS::io::DI::logiBUS_IXA`

- **Parameters**: `QI = TRUE`, `Input = Input_I1`

- **Explanation**: Connects the physical button input `I1` to the adapter interface.

- **DigitalInput_CLK_I2**: logiBUS input with click detection for the toggle switch

- **Type**: `logiBUS::io::DI::logiBUS_IE`

- **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`

- **Explanation**: Detects a single click on `I2` and reports it as an event `IND`.

- **AX_ASRT_RF_TRIG_1**: Source 1 — classic push button with last-wins behavior

- **Type**: `MyLib::sys::AX_ASRT_RF_TRIG` (SubApp instance)

- **Parameters**: none

- **Explanation**: Press (rising edge) → `SET`, Release (falling edge) → `RESET`, output as a bundled `ASRT` signal. `TOGGLE` never fires here — not as a general rule, but because `AX_ASRT_RF_TRIG`'s internal `TOGGLE_IN` remains unwired (see "Sub-Blocks" below).

- **ASRT_3EVENTS_TO_SRT_1**: Source 2 — true toggle switch

- **Type**: `adapter::conversion::unidirectional::ASRT_3EVENTS_TO_SRT`

- **Parameters**: none

- **Explanation**: Only the `TOGGLE` input is wired (fed by `I2` and `BUTTON_SINGLE_CLICK`), `SET` and `RESET` remain unused. This generates a bundled `ASRT` signal that carries only the TOGGLE rail.

- **ASRT_MERGE_2**: Merges the two ASRT sources

- **Type**: `adapter::events::unidirectional::ASRT_MERGE_2`

- **Parameters**: None

- **Explanation**: Combines both ASRT streams into a single signal — the same OR merge as `ASR_MERGE_2` to `Uebung_230_AX.md`, now including the third (toggle) rail.

- **ASRT_AX_T_FF_SR_1**: Common Set-Reset-Toggle Interlock

- **Type**: `adapter::events::unidirectional::ASRT_AX_T_FF_SR`

- **Parameters**: None

- **Explanation**: Receives the combined SET/RESET/TOGGLE signal via the `S_R_T` socket and uses it to control the output state `Q`.

- **DigitalOutput_Q1**: logiBUS digital output

- **Type**: `logiBUS::io::DQ::logiBUS_QXA`

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`

- **Description**: Passes the state of `ASRT_AX_T_FF_SR_1.Q` to the physical output `Q1`.


### Sub-Blocks: `AX_ASRT_RF_TRIG` (Composite Sub-App)

`AX_ASRT_RF_TRIG` is a composite sub-app within `MyLib_AX-1.0.0/typelib/sys/AX_ASRT_RF_TRIG.SUB` and was created from existing blocks instead of being a new low-level function block type: the existing `AX_ASR_RF_TRIG` (edge detection with ASR output, see `Uebung_230_AX.md`) plus `ASRT_SR_AE_TO_SRT`, whose `SR_IN` is fed directly from the ASR output and whose `TOGGLE_IN` remains unwired. This results in an ASRT signal that only carries SET/RESET and never fires TOGGLE.


## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_ASRT_RF_TRIG_1.QI` (AdapterConnection): The physical button state of `I1` feeds Source 1.

2. `DigitalInput_CLK_I2.IND` → `ASRT_3EVENTS_TO_SRT_1.TOGGLE` (EventConnection): Each single click on `I2` triggers a TOGGLE event in Source 2.

3. `AX_ASRT_RF_TRIG_1.Q` → `ASRT_MERGE_2.IN1` and `ASRT_3EVENTS_TO_SRT_1.ASRT_OUT` → `ASRT_MERGE_2.IN2` (AdapterConnections): Both ASRT sources are routed to the merge block.

4. `ASRT_MERGE_2.OUT` → `ASRT_AX_T_FF_SR_1.S_R_T`: The combined SET/RESET/TOGGLE signal controls the common latch.

5. `ASRT_AX_T_FF_SR_1.Q` → `DigitalOutput_Q1.OUT`: The latch state is set to the physical output `Q1`.

**Behavior**: `I1` still provides the same last-wins behavior as in `Uebung_229_AX.md`/`Uebung_230_AX.md`: pressing triggers `SET` (`Q1` → ON), releasing triggers `RESET` (`Q1` → OFF). The toggle button `I2` acts independently of this: every click on `I2` immediately toggles the current state of `Q1`, regardless of the physical state of `I1`. Concretely, that means: if `I1` is held pressed (so `Q1` is currently ON via `SET`), a click on `I2` can still switch `Q1` OFF even though `I1` remains pressed — `Q1` does not permanently track `I1`'s held state, but always follows whichever SET, RESET, or TOGGLE event arrived last, regardless of which of the two sources it came from.

## Summary

Exercise 232 combines two fundamentally different operating philosophies on a single latch: a classic last-wins button (SET/RESET using edges, as in `Uebung_229_AX.md`/`Uebung_230_AX.md`) and a true toggle button (TOGGLE only, triggered by a single click), merged via `ASRT_MERGE_2` to `ASRT_AX_T_FF_SR`. It demonstrates that the ASR adapter technique from the previous exercises can be extended to include a third, independent toggle rail without fundamentally altering the existing merge logic, and that different operating concepts can thus be cleanly integrated onto a common output.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
