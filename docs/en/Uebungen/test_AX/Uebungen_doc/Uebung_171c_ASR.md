# Exercise_171c_ASR: SET/RESET Latch with logiBUS_IEA (AE adapter already installed)

![Uebung_171c_ASR_network](./Uebung_171c_ASR_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements the same function as `Uebung_171b_ASR` (button `I1` = SET click, button `I2` = RESET click, latching via `ASR_AX_SR`), but eliminates the manually wired `AE_EVENT_TO_E` bridge per channel: the input block `logiBUS_IEA` provides the `AE` adapter plug already installed, just like `logiBUS_IX` → `logiBUS_IXA` for level signals, for event inputs only.


## Function Blocks (FBs) Used

- **DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**: logiBUS click inputs with integrated AE adapter (Type: `logiBUS::io::DI::logiBUS_IEA`)

- **Parameters**: `QI = TRUE`, `Input = Input_I1` or `Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`

- **Explanation**: Internally encapsulates a `logiBUS_IE` and redirects its click event directly to a dedicated `AE` adapter output (`IN`) – the bridge that is still separate in `Uebung_171b_ASR` The functionality that was previously required for the `AE_EVENT_TO_E` module is already integrated into the input module itself.

- **ASR_2AE_TO_SR_1**: AE-to-ASR converter (Type: `adapter::conversion::unidirectional::ASR_2AE_TO_SR`)

- **Parameters**: None

- **Explanation**: Accepts two `AE` adapter inputs (`SET_IN`, `RESET_IN`) and combines them into a single `ASR` adapter output (`ASR_OUT`).


- **ASR_AX_SR_1**: Set-Reset-Latch (Type: `adapter::events::unidirectional::ASR_AX_SR`)

- **Parameters**: None

- **Explanation**: Locks the bundled `S_R` input. Set-dominant: `S` turns `Q` ON, `R` turns `Q` OFF.

- **DigitalOutput_Q1**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`

- **Explanation**: Outputs the latch state `Q` as a physical output signal.

### Sub-Blocks: None

This exercise does not use any further sub-blocks; all function blocks are located directly at the top level of the sub-app.


## Program Flow and Connections

1. **Click on I1 (SET)**: `DigitalInput_CLK_I1` (`logiBUS_IEA`) detects the click internally and provides it directly as the `AE` adapter output (`IN`) – without a separate intermediate block.

2. **Click on I2 (RESET)**: Similarly, `DigitalInput_CLK_I2.IN` provides the reset branch as the `AE` adapter.

3. **Bundling to ASR**: `DigitalInput_CLK_I1.IN` goes directly to `ASR_2AE_TO_SR_1.SET_IN`, and `DigitalInput_CLK_I2.IN` to `ASR_2AE_TO_SR_1.RESET_IN`. The function block combines both into `ASR_OUT`.

4. **Locking**: `ASR_2AE_TO_SR_1.ASR_OUT` is routed to `ASR_AX_SR_1.S_R`. The latch switches to `Q = TRUE` on SET and to `Q = FALSE` on RESET.

5. **Output**: `ASR_AX_SR_1.Q` directly drives `DigitalOutput_Q1.OUT`.


## Summary

Exercise 171c exhibits the exact same latching behavior as Exercises 171 and 171b, but requires the fewest components: `logiBUS_IEA` replaces `logiBUS_IE` and includes the `AE` adapter plug, eliminating the need for the `AE_EVENT_TO_E` components required in Exercise 171b. A comparison of the three sub-apps in the 4diac editor reveals the same functional core (click I1 → `Q1` ON, click I2 → `Q1` OFF) with progressively more compact wiring.


---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
