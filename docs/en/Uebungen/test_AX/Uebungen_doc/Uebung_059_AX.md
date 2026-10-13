# Exercise_059_AX: Pure Event Step Chain 8-Channel (Adapter Version)

![Uebung_059_AX_network](./Uebung_059_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements a purely event-driven step chain using AX adapter technology: a sequence of 8 outputs (`Q1` to `Q8`) in which every step is advanced exclusively by an external event (button click) - no automatic time control. Since only the physical buttons `I1` to `I4` are available but up to 8 transitions are needed, one counter each counts the clicks of `I2` and `I3`, and a downstream demultiplexer fans the counter value out to the individual transitions of the sequencer. In this way 8 state transitions can be triggered with two buttons, without needing a separate button for every step.

## Function Blocks (FBs) Used

- **DigitalInput_CLK_I1** ... **DigitalInput_CLK_I4**: click inputs
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1` ... `Input_I4`, each with `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Report a single click on `I1` to `I4` as event `IND`.
- **sequence_block**: sequencer
    - **Type**: [`logiBUS::utils::sequence::event::sequence_E_08_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/utils/sequence/event/sequence_E_08_AX/)
    - **Parameters**: none
    - **Explanation**: Purely event-driven sequence with 8 states. The outputs `DO_S1` ... `DO_S8` are AX adapters, the step number is available at the adapter `STATE_NR`. The inputs `START_S1`, `S1_S2` ... `S7_S8`, `S8_START` and `RESET` control the transitions.
- **E_CTU_0**, **E_CTU_1**: event counters
    - **Type**: [`adapter::events::unidirectional::AUI_CTU`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_CTU/)
    - **Parameters**: none
    - **Explanation**: Counts up on every event `CU` and outputs the counter value via the adapter `CV`; `R` resets the counter.
- **initval_1**: start value 1
    - **Type**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameters**: `INIT_VAL = 1`
    - **Explanation**: Provides the constant value 1 as an AUI signal.
- **AUI_SPLIT_2**: signal splitter
    - **Type**: [`adapter::events::unidirectional::AUI_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: Distributes the value 1 from `initval_1` to both subtractors.
- **F_SUB_0**, **F_SUB_1**: subtractors
    - **Type**: [`adapter::iec61131::arithmetic::AUI_SUB_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/arithmetic/AUI_SUB_2/)
    - **Parameters**: none
    - **Explanation**: Form `IN1 - IN2`, here the counter value minus 1. The first click thus corresponds to index 0.
- **E_DEMUX_0**, **E_DEMUX_1**: event demultiplexers
    - **Type**: [`adapter::events::unidirectional::AUI_DEMUX_4`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_DEMUX_4/)
    - **Parameters**: none
    - **Explanation**: Forwards the event to `EO1` (index 0), `EO2` (1), `EO3` (2) or `EO4` (3), depending on the index at the adapter `K`.
- **AS_TO_AUDI**: conversion adapter
    - **Type**: [`adapter::conversion::unidirectional::AS_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AS_AUDI/AS_TO_AUDI/)
    - **Parameters**: none
    - **Explanation**: Converts the step number from the AS adapter into an AUDI adapter.
- **Q_NumericValue_AUDI**: VT display of the step number
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = OutputNumber_N1`
    - **Explanation**: Shows the current step number on the ISOBUS terminal.
- **DigitalOutput_Q1** ... **DigitalOutput_Q8**: digital outputs
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1` ... `Output_Q8`
    - **Explanation**: Output the state of the respective step at the physical outputs `Q1` to `Q8`.

## Program Flow and Connections

1. `DigitalInput_CLK_I1.IND` → `sequence_block.START_S1`: A click on `I1` starts the sequence in step 1.
2. `DigitalInput_CLK_I2.IND` → `E_CTU_0.CU`: The clicks on `I2` are counted. `E_CTU_0.CV` → `F_SUB_0.IN1` and `AUI_SPLIT_2.OUT1` → `F_SUB_0.IN2` (value 1), `F_SUB_0.OUT` → `E_DEMUX_0.K`: The index is the counter value minus 1.
3. `E_DEMUX_0.EO1` → `sequence_block.S1_S2`, `E_DEMUX_0.EO2` → `S2_S3`, `E_DEMUX_0.EO3` → `S3_S4`, `E_DEMUX_0.EO4` → `S4_S5`: The 1st to 4th click on `I2` advances from step 1 to 2, 2 to 3, 3 to 4 and 4 to 5 respectively.
4. `E_DEMUX_0.EO4` → `E_CTU_0.R`: After the 4th click the counter `E_CTU_0` is reset.
5. `DigitalInput_CLK_I3.IND` → `E_CTU_1.CU`, `E_CTU_1.CV` → `F_SUB_1.IN1`, `AUI_SPLIT_2.OUT2` → `F_SUB_1.IN2`, `F_SUB_1.OUT` → `E_DEMUX_1.K`: Same principle for the clicks on `I3`.
6. `E_DEMUX_1.EO1` → `sequence_block.S5_S6`, `EO2` → `S6_S7`, `EO3` → `S7_S8`, `EO4` → `S8_START`: The 1st to 4th click on `I3` advances from step 5 to 6, 6 to 7, 7 to 8, and from 8 back to the start. `E_DEMUX_1.EO4` → `E_CTU_1.R` also resets this counter.
7. `DigitalInput_CLK_I4.IND` → `sequence_block.RESET`, `E_CTU_0.R` and `E_CTU_1.R`: A click on `I4` resets the sequence and both counters.
8. `sequence_block.DO_S1` ... `DO_S8` → `DigitalOutput_Q1.OUT` ... `DigitalOutput_Q8.OUT` (AdapterConnections): The active step switches the associated output.
9. `sequence_block.STATE_NR` → `AS_TO_AUDI.AS_IN` and `AS_TO_AUDI.AUDI_OUT` → `Q_NumericValue_AUDI.u32NewValue`: The step number is shown on the VT.

**Behavior**: A click on `I1` → step 1, `Q1` active. Every further click on `I2` (steps 1 to 5) or `I3` (steps 5 to 8 and back to the start) advances to the next step. A click on `I4` resets everything.

## Summary

The exercise shows a purely event-driven 8-step chain in AX adapter technology. With one counter and one demultiplexer per button (`I2`, `I3`), 8 transitions are realized with only two advance buttons; `I1` starts and `I4` resets.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
