# Exercise_007a3_AX: Turn Signal with E_CYCLE, E_SWITCH, and E_SR

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/041f4df4-b729-484d-b786-b6dcdf151961)

![Uebung_007a3_AX_network](./Uebung_007a3_AX_network.svg)

* * * * * * * * * *

## Introduction

This article describes the logiBUS® exercise `Uebung_007a3_AX`. This exercise demonstrates the "clean" solution for a switchable blinker. The goal is that the blinker always stops in the "OFF" state (advantage according to the comment in the network: "this blinker always stays OFF").

## Function Blocks (FBs) Used

- **START**: logiBUS input with click detection for starting
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I1` as event `IND`.
- **STOP**: logiBUS input with click detection for stopping
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I2` as event `IND`.
- **E_CYCLE**: Clock generator
    - **Type**: `iec61499::events::E_CYCLE`
    - **Parameters**: `DT = T#1s`
    - **Explanation**: After `START`, generates `EO` events at one-second intervals until `STOP` arrives.
- **E_SWITCH**: Event switch
    - **Type**: `adapter::events::unidirectional::AX_E_SWITCH`
    - **Parameters**: none
    - **Explanation**: Forwards each clock event `EI` either to `EO0` or to `EO1`, depending on the state supplied via the adapter `G`.
- **E_SR**: Memory ("blinker is on")
    - **Type**: `adapter::events::unidirectional::AX_SR`
    - **Parameters**: none
    - **Explanation**: Set-reset memory with inputs `S` and `R` and output `Q` (adapter).
- **AX_SPLIT_2**: Signal distributor
    - **Type**: `adapter::events::unidirectional::AX_SPLIT_2`
    - **Parameters**: none
    - **Explanation**: Distributes the state of `E_SR.Q` to the lamp (`OUT1`) and to the feedback to `E_SWITCH.G` (`OUT2`).
- **DigitalOutput_Q1**: logiBUS digital output (the lamp)
    - **Type**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Passes the state of the memory to the physical output `Q1`.

## Program Flow and Connections

1. `START.IND` → `E_CYCLE.START`: the click on `I1` starts the clock generator.
2. `STOP.IND` → `E_CYCLE.STOP`: the click on `I2` stops the clock generator.
3. `E_CYCLE.EO` → `E_SWITCH.EI`: every clock pulse enters the event switch.
4. `E_SWITCH.EO0` → `E_SR.S` and `E_SWITCH.EO1` → `E_SR.R`: depending on the switch position, the memory is set or reset.
5. `E_SR.Q` → `AX_SPLIT_2.IN`: the memory state is distributed.
6. `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT`: the lamp `Q1` follows the memory.
7. `AX_SPLIT_2.OUT2` → `E_SWITCH.G`: feedback. If the memory is set, the next clock pulse resets it; if it is reset, the next clock pulse sets it. The lamp therefore toggles every second.
8. `STOP.IND` → `E_SR.R`: **in addition** to stopping the clock generator, the memory is reset directly.

**Behavior**: A click on `I1` starts the blinking, a click on `I2` stops it. Because `STOP` additionally drives `E_SR.R` directly, `Q` and thus the lamp `Q1` is **forcibly** set to FALSE when stopping - the blinker always stays OFF.

## Summary

The exercise solves the problem that a simple blinker could get stuck in the "ON" state when stopped. By combining `E_CYCLE`, the feedback switch `E_SWITCH` and the memory `E_SR`, plus the additional direct reset of `E_SR` by `STOP`, the blinker is guaranteed to end in the OFF state.

**Application example - professional indicator**: An industrial fault indicator must flash when a fault is present and be **off** when the fault has been acknowledged. It must never remain illuminated permanently (this would often indicate "operational" or something else).

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
