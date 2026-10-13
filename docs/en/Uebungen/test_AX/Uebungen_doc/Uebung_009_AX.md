# Exercise_009_AX: Ticker (AX Adapter Based)

![Uebung_009_AX_network](./Uebung_009_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise demonstrates an adapter-based ticker: a clock generator makes an output blink once per second, every second clock edge (output goes TRUE) is counted by an up/down counter, and the counter value is displayed as a number on the ISOBUS terminal. Through the `AX`/`AUDI` adapters, event and data flow are each bundled in a single connection.

## Function Blocks (FBs) Used

- **INIT**: Start trigger
    - **Type**: [`iec61131::booleanOperators::INIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/iec61131-3-bool/booleanOperators/INIT/)
    - **Parameters**: none
    - **Explanation**: Forwards `INIT` without delay as `INITO` and `REQ` as `CNF`. `INITO` is fed back to `REQ`, `CNF` starts the clock generator.
- **E_CYCLE**: Clock generator
    - **Type**: [`iec61499::events::E_CYCLE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_CYCLE/)
    - **Parameters**: `DT = T#1s`
    - **Explanation**: After start (`START`), generates an event `EO` every second.
- **E_SWITCH**: Adapter-based event switch
    - **Type**: [`adapter::events::unidirectional::AX_E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_E_SWITCH/)
    - **Parameters**: none
    - **Explanation**: Forwards the clock event `EI` to `EO0` (`G` = FALSE) or `EO1` (`G` = TRUE), depending on the value of the adapter `G`.
- **E_SR**: Adapter-based memory element (blinker state)
    - **Type**: [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/)
    - **Parameters**: none
    - **Explanation**: `S` (from `E_SWITCH.EO0`) sets, `R` (from `E_SWITCH.EO1`) resets. The state is output via the adapter output `Q`.
- **SPLIT_1**: Signal distributor
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_3/)
    - **Parameters**: none
    - **Explanation**: Distributes the `AX` signal from `E_SR.Q` to three targets: `OUT1` (digital output), `OUT2` (count enable) and `OUT3` (feedback to the switch).
- **E_PERMIT**: Adapter-based event gate
    - **Type**: [`adapter::events::unidirectional::AX_PERMIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_PERMIT/)
    - **Parameters**: none
    - **Explanation**: Triggers `EO` only if an event (`PERMIT.E1`) arrives at the adapter `PERMIT` and `PERMIT.D1` is `TRUE`.
- **E_CTUD_UDINT**: Adapter-based up/down counter
    - **Type**: [`adapter::events::unidirectional::AUDI_CTUD_UDINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UDINT/AUDI_CTUD_UDINT/)
    - **Parameters**: none
    - **Explanation**: Increases the count value by 1 on every `CU` event and outputs it via the `AUDI` adapter `CV`.
- **Q_NumericValue**: Display on the ISOBUS terminal
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = OutputNumber_N1`
    - **Explanation**: Receives the count value via the `AUDI` adapter `u32NewValue` and shows it in the object `OutputNumber_N1`.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Passes the blinker state to the physical output `Q1`.

## Program Flow and Connections

1. `INIT.INITO` → `INIT.REQ` and `INIT.CNF` → `E_CYCLE.START` (EventConnections): The clock generator is started on initialization.
2. `E_CYCLE.EO` → `E_SWITCH.EI` (EventConnection): Every second a clock event arrives at the switch.
3. `E_SWITCH.EO0` → `E_SR.S` and `E_SWITCH.EO1` → `E_SR.R` (EventConnections): If the memory element is reset, the clock sets it, otherwise the clock resets it. The state therefore changes with every clock.
4. `E_SR.Q` → `SPLIT_1.IN` (AdapterConnection): The state is distributed to three targets.
5. `SPLIT_1.OUT3` → `E_SWITCH.G` (AdapterConnection): The current state controls the switch and thereby causes the toggling.
6. `SPLIT_1.OUT1` → `DigitalOutput_Q1.OUT` (AdapterConnection): `Q1` blinks in the rhythm of the state.
7. `SPLIT_1.OUT2` → `E_PERMIT.PERMIT` (AdapterConnection): On every state change an event arrives at the gate; it is only passed on if the state is `TRUE`.
8. `E_PERMIT.EO` → `E_CTUD_UDINT.CU` (EventConnection): Every event passed on counts the counter up by 1.
9. `E_CTUD_UDINT.CV` → `Q_NumericValue.u32NewValue` (AdapterConnection): The count value is sent to the ISOBUS terminal.

**Behavior**: `Q1` is alternately on for one second and off for one second. The counter is incremented each time the output switches on, i.e. once every two seconds; on the terminal, the number in `OutputNumber_N1` rises steadily.

## Summary

This exercise shows the advantages of consistently adapter-based development: clock, blinker state, counter and display are wired with a few adapter connections because event and data flow are transferred bundled. `AX_SPLIT_3` distributes the blinker state simultaneously to the output, the count enable and the feedback.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
