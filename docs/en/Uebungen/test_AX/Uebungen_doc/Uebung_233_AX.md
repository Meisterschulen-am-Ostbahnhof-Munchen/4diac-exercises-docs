# Exercise_233_AX: 3 Buttons via ASR_MERGE_3 to 1 Shared SR Latch (Last Wins)

![Uebung_233_AX_network](./Uebung_233_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the direct continuation of `Uebung_230_AX` ("2 Buttons, Last Wins" via `ASR_MERGE_2`): here, **three** independent buttons are combined into a single latch via `ASR_MERGE_3`. It demonstrates that the MERGE family (`ASR_MERGE_2..7`) is not a purely 2-input special case, but scales for several sources up to seven inputs.


## Function Blocks (FBs) Used

- **DigitalInput_I1**, **DigitalInput_I2**, **DigitalInput_I3**: `logiBUS::io::DI::logiBUS_IXA`

- **Parameters**: `QI = TRUE`, `Input = Input_I1` / `Input_I2` / `Input_I3`

- **Explanation**: Connect the three physical pushbuttons to the adapter interfaces of the subsequent `AX_ASR_RF_TRIG` function blocks.

- **AX_ASR_RF_TRIG_1**, **AX_ASR_RF_TRIG_2**, **AX_ASR_RF_TRIG_3**: `adapter::events::unidirectional::AX_ASR_RF_TRIG`

- **Parameters**: none

- **Explanation**: Each monitors a button and converts the rising edge (press) and falling edge (release) into a bundled `ASR` signal (`SET`/`RESET`).

- **ASR_MERGE_3**: `adapter::events::unidirectional::ASR_MERGE_3`

- **Parameters**: none

- **Explanation**: Combines all three `ASR` streams into one—structurally identical to `ASR_MERGE_2` from `Uebung_230_AX`, except with a third socket (`IN3`). The operation remains a simple OR operation across all sources.

- **ASR_AX_SR**: `adapter::events::unidirectional::ASR_AX_SR`

- **Parameters**: none

- **Explanation**: Latches the merged signal as in 229/230/233: Last-Wins—which of the three buttons was last pressed or released determines the state of `Q1`.


- **DigitalOutput_Q1**: `logiBUS::io::DQ::logiBUS_QXA`

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`

- **Explanation**: Outputs the latch state as a physical output signal.

### Sub-Blocks: None

This exercise uses only direct FB instances, not SubApp instances.

## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_ASR_RF_TRIG_1.QI`, `DigitalInput_I2.IN` → `AX_ASR_RF_TRIG_2.QI`, `DigitalInput_I3.IN` → `AX_ASR_RF_TRIG_3.QI`: The three buttons each feed their own edge detector.

2. `AX_ASR_RF_TRIG_1.Q` → `ASR_MERGE_3.IN1`, `AX_ASR_RF_TRIG_2.Q` → `ASR_MERGE_3.IN2`, `AX_ASR_RF_TRIG_3.Q` → `ASR_MERGE_3.IN3`: All three bundled `ASR` signals converge in the merge block.

3. `ASR_MERGE_3.OUT` → `ASR_AX_SR.S_R`: The merged signal controls the common SR latch.

4. `ASR_AX_SR.Q` → `DigitalOutput_Q1.OUT`: The latch state is switched to the physical output `Output_Q1`.

5. Result: Each of the three buttons can switch `Q1` on and off any number of times in any sequence — Last Wins, as already described in 229/230. Compared to `Uebung_230_AX`, the pattern is identical, only extended by a third source.

## Summary

`Uebung_233_AX` demonstrates that the Last-Wins Latch pattern known from 229/230 can be extended losslessly to a third independent source by simply replacing `ASR_MERGE_2` with `ASR_MERGE_3` (plus an additional `AX_ASR_RF_TRIG`). The MERGE family (`ASR_MERGE_2` to `ASR_MERGE_7`) thus scales to several sources up to seven inputs without changing the underlying OR logic or latch behavior—an important building block for constructing modular, easily extensible control logic.


---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
