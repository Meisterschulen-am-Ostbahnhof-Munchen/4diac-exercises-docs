# Exercise_231_AX: A Button via ASR_SPLIT_2 to Two Independent Latches (Fan-out)

![Uebung_231_AX_network](./Uebung_231_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the mirror image of `Uebung_230_AX.md`: there, `ASR_MERGE_2` combined two sources into a single latch (N→1, fan-in). Here, exactly one button (`I1`) controls two independent latches simultaneously via `ASR_SPLIT_2` (1→N, fan-out). The exercise demonstrates that an adapter plug is strictly point-to-point and therefore—exactly the mirror image of the merge case—a split block is needed to distribute a single ASR stream to multiple destinations without loss.


## Function Blocks (FBs) Used

- **DigitalInput_I1**: logiBUS digital input

- **Type**: `logiBUS::io::DI::logiBUS_IXA`

- **Parameters**: `QI = TRUE`, `Input = Input_I1`

- **Explanation**: Connects the physical button input to the adapter interface.

- **AX_ASR_RF_TRIG_1**: Edge detection with ASR output

- **Type**: `adapter::events::unidirectional::AX_ASR_RF_TRIG`

- **Parameters**: none

- **Explanation**: Press button (rising edge) → `SET`, release (falling edge) → `RESET`, as a single bundled `ASR` signal — the same function block as in `Uebung_230_AX.md`.

- **ASR_SPLIT_2**: Distributor for the single ASR stream

- **Type**: `adapter::events::unidirectional::ASR_SPLIT_2`

- **Parameters**: None

- **Explanation**: An adapter plug (`AX_ASR_RF_TRIG_1.Q`) is strictly point-to-point—it cannot be directly connected to two `ASR_AX_SR.S_R` sockets simultaneously. The same fundamental rule that necessitated `ASR_MERGE_2` in `Uebung_230_AX.md` (multiple sources cannot directly target one adapter socket — unlike the raw `EventConnections` in `Uebung_229_AX.md`, where that is allowed) here, conversely, enforces `ASR_SPLIT_2` (one source cannot directly serve two plugs simultaneously). `ASR_SPLIT_2` losslessly duplicates the single ASR stream to two outputs.

- **ASR_AX_SR_1**, **ASR_AX_SR_2**: two independent set/reset latches

- **Type**: `adapter::events::unidirectional::ASR_AX_SR`

- **Parameters**: none

- **Explanation**: Two separate instances of the same latch block as in `Uebung_230_AX.md`. Both receive exactly the same SET/RESET commands at exactly the same time via `ASR_SPLIT_2` and therefore run synchronously, even though they are two completely independent blocks, each with its own output—there is no direct connection between them.

- **DigitalOutput_Q1**, **DigitalOutput_Q2**: logiBUS digital outputs

- **Type**: `logiBUS::io::DQ::logiBUS_QXA`

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`, and `Output_Q2`

- **Explanation**: Pass the states of `ASR_AX_SR_1.Q` and `ASR_AX_SR_2.Q`, respectively, to the physical outputs.

### Sub-Blocks: None

This exercise does not use any further sub-blocks; all function blocks are located directly at the top level of the sub-app.



## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_ASR_RF_TRIG_1.QI`: The physical button state is passed to the edge detection.

2. `AX_ASR_RF_TRIG_1.Q` → `ASR_SPLIT_2.IN`: The bundled SET/RESET signal is sent to the distributor.

3. `ASR_SPLIT_2.OUT1` → `ASR_AX_SR_1.S_R` and `ASR_SPLIT_2.OUT2` → `ASR_AX_SR_2.S_R`: The same ASR stream is duplicated losslessly to both latches.

4. `ASR_AX_SR_1.Q` → `DigitalOutput_Q1.OUT` and `ASR_AX_SR_2.Q` → `DigitalOutput_Q2.OUT`: Both latch states are assigned to their respective physical outputs.

**Behavior**: Pressing and holding `I1` turns `Q1` AND `Q2` ON simultaneously. Releasing `I1` turns both OFF simultaneously. `ASR_AX_SR_1` and `ASR_AX_SR_2` are two separate instances coupled only via the common `ASR_SPLIT_2` origin—there is no direct connection between them.

## Summary

Exercise 231 demonstrates the mirror image of `Uebung_230_AX.md`: while there, multiple sources were combined onto a common latch via `ASR_MERGE_2` (fan-in), here, `ASR_SPLIT_2` distributes a single source across multiple independent latches (fan-out). Both exercises together illustrate the fundamental point-to-point nature of adapter plugs and sockets in 4diac and the provided split/merge components that cleanly overcome this limitation.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
