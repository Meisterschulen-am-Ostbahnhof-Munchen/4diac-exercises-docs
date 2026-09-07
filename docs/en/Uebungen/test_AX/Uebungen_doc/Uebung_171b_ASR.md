# Exercise_171b_ASR: SET/RESET Latch via AE Adapter instead of Raw Events

![Uebung_171b_ASR_network](./Uebung_171b_ASR_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements the same function as `Uebung_171_ASR` (button `I1` = SET click, button `I2` = RESET click, latching via `ASR_AX_SR`), but deliberately uses **AE adapter sockets** to wire the path from the two click events to the latch instead of raw event connections. This demonstrates why the AE adapter type exists at all: a `AE` plug is a fully functional adapter that, like `AX`/`ASR`/`ASRT`, can be further processed with generic building blocks (e.g., `AE_SPLIT_2`) – a raw event cannot do this; it can only be wired via EventConnections.



## Function Blocks (FBs) Used

- **DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**: logiBUS click inputs (Type: `logiBUS::io::DI::logiBUS_IE`)

- **Parameters**: `QI = TRUE`, `Input = Input_I1`, `Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`

- **Explanation**: Report a single button click as an event (`IND`) at the respective physical input.


**DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**: logiBUS click inputs (Type: `logiBUS::io::DI::logiBUS_IE`)

**Explanation**: Report a single button click as an event (`IND`) at the respective physical input. - **AE_EVENT_TO_E_SET**, **AE_EVENT_TO_E_RESET**: Event-to-adapter bridge (Type: `adapter::conversion::unidirectional::AE_EVENT_TO_E`)

- **Parameters**: None

- **Explanation**: Converts a raw click event (`REQ`) into a typed `AE` adapter output (`AE_OUT`). This allows the event to be passed on like an adapter instead of just as a loose EventConnection.


- **ASR_2AE_TO_SR_1**: AE-to-ASR Converter (Type: `adapter::conversion::unidirectional::ASR_2AE_TO_SR`)

- **Parameters**: None

- **Description**: Accepts two `AE` adapter inputs (`SET_IN`, `RESET_IN`) and combines them into a single `ASR` adapter output (`ASR_OUT`).

- **ASR_AX_SR_1**: Set-Reset-Latch (Type: `adapter::events::unidirectional::ASR_AX_SR`)

- **Parameters**: None

- **Explanation**: Locks the bundled `S_R` input. Set-dominant: `S` turns `Q` ON, `R` turns `Q` OFF.

- **DigitalOutput_Q1**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`

- **Explanation**: Outputs the latch state `Q` as a physical output signal.

### Sub-Blocks: None

This exercise does not use any further sub-blocks; all function blocks are located directly at the top level of the sub-app.

## Program Flow and Connections

1. **Click on I1 (SET)**: `DigitalInput_CLK_I1.IND` triggers `AE_EVENT_TO_E_SET.REQ` via an EventConnection. This converts the raw event into a `AE` adapter output (`AE_OUT`).

2. **Click on I2 (RESET)**: Similarly, `DigitalInput_CLK_I2.IND` triggers `AE_EVENT_TO_E_RESET.REQ`, whose `AE_OUT` handles the reset branch.

3. **Bundling to ASR**: `AE_EVENT_TO_E_SET.AE_OUT` goes to `ASR_2AE_TO_SR_1.SET_IN` via an adapter connection, and `AE_EVENT_TO_E_RESET.AE_OUT` goes to `ASR_2AE_TO_SR_1.RESET_IN`. The function block combines both into a single `ASR_OUT`.

4. **Locking**: `ASR_2AE_TO_SR_1.ASR_OUT` is routed to `ASR_AX_SR_1.S_R`. The latch switches to `Q = TRUE` on SET and to `Q = FALSE` on RESET.

5. **Output**: `ASR_AX_SR_1.Q` directly drives `DigitalOutput_Q1.OUT`.


## Summary

Exercise 171b demonstrates the same SET/RESET latch as Exercise 171, but deliberately uses AE adapter sockets instead of raw events: `AE_EVENT_TO_E` bridges the click event to the typed `AE` plug for each channel, and `ASR_2AE_TO_SR` combines both into a `ASR` signal for the latch `ASR_AX_SR`. The additional effort compared to the raw event variant is worthwhile as soon as the signal itself needs to be further processed like an adapter (e.g., duplicated with `AE_SPLIT_2`) – something that would not be possible with a raw event.


`AE_EVENT_TO_E` bridges the click event to the typed `AE` plug for each channel, and `ASR_2AE_TO_SR` combines both into a `ASR` signal for the latch `ASR_AX_SR`. ---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
