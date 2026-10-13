# Exercise_008_AX: Flasher with E_CYCLE, E_SWITCH and E_SR

![Uebung_008_AX_network](./Uebung_008_AX_network.svg)

* * * * * * * * * *

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/041f4df4-b729-484d-b786-b6dcdf151961)

## Introduction

Implementation of a continuously running flasher as a basic building block. A cyclic clock (`E_CYCLE`, 1 s) feeds a switch (`E_SWITCH`) that alternately triggers setting and resetting of a set/reset latch (`E_SR`). The latch state is applied to output `Q1` and is also fed back to the switch as its selector signal. Since no external intervention is provided, the output flashes permanently as long as the controller is running.

## Function Blocks (FBs) Used

- **INIT**: start trigger
    - **Type**: [`iec61131::booleanOperators::INIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/iec61131-3-bool/booleanOperators/INIT/)
    - **Parameters**: none
    - **Explanation**: Passes events through during the initialization phase. The output `INITO` is fed back to its own input `REQ`; the resulting confirmation `CNF` starts the clock.
- **E_CYCLE**: clock generator
    - **Type**: [`iec61499::events::E_CYCLE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_CYCLE/)
    - **Parameters**: `DT = T#1s`
    - **Explanation**: After being started, generates an event `EO` every second.
- **E_SWITCH**: switch
    - **Type**: [`adapter::events::unidirectional::AX_E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_E_SWITCH/)
    - **Parameters**: none
    - **Explanation**: Forwards the event `EI` to `EO0` (`G` = 0) or `EO1` (`G` = 1), depending on the selector signal at the adapter `G`.
- **E_SR**: set/reset latch
    - **Type**: [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/)
    - **Parameters**: none
    - **Explanation**: The event `S` sets the output `Q` to TRUE, `R` sets it to FALSE.
- **SPLIT_1**: signal splitter
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: Distributes the signal from the latch `E_SR.Q` to two outputs: `OUT1` for output `Q1`, `OUT2` as feedback to the switch.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Passes the latch state to the physical output `Q1`.

## Program Flow and Connections

1. `INIT.INITO` → `INIT.REQ` (EventConnection): The initialization confirmation triggers the service request on the same block.
2. `INIT.CNF` → `E_CYCLE.START` (EventConnection): The clock is started.
3. `E_CYCLE.EO` → `E_SWITCH.EI` (EventConnection): Every clock pulse (every 1 s) enters the switch.
4. `E_SWITCH.EO0` → `E_SR.S` and `E_SWITCH.EO1` → `E_SR.R` (EventConnections): With `G` = 0 the latch is set, with `G` = 1 it is reset.
5. `E_SR.Q` → `SPLIT_1.IN` (AdapterConnection): The latch state enters the splitter.
6. `SPLIT_1.OUT1` → `DigitalOutput_Q1.OUT` (AdapterConnection): The state is applied to the physical output `Q1`.
7. `SPLIT_1.OUT2` → `E_SWITCH.G` (AdapterConnection): The state is fed back to the switch as selector signal.

**Behavior**: If the latch is reset (`Q` = FALSE, `G` = 0), the next clock pulse sets it via `EO0`/`S`; if it is set (`G` = 1), the next pulse resets it via `EO1`/`R`. Output `Q1` therefore changes its state every second.

## Application Example

**Heartbeat**: An LED that flashes on the circuit board or in the control cabinet to indicate: "The CPU is still alive and the program is running".

## Summary

The exercise shows a flasher built from standard blocks: `E_CYCLE` supplies the clock, `E_SWITCH` and `E_SR` form a toggle logic together with the feedback via `SPLIT_1`, and `INIT` starts the clock after initialization.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
