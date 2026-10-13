# Exercise_020a_AX: DigitalInput_I1 to DigitalOutput_Q1 via AX_SR (Buffer)

![Uebung_020a_AX_network](./Uebung_020a_AX_network.svg)

* * * * * * * * * *

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/041f4df4-b729-484d-b786-b6dcdf151961)

## Introduction

This exercise passes a digital input on to a digital output via a set/reset latch (`AX_SR`). In contrast to [Uebung_001_AX](./Uebung_001_AX.md), which uses a direct connection, the edges of the input are explicitly converted into events here: the rising edge sets the latch, the falling edge resets it. As a result the circuit behaves like a direct connection, but internally uses event-based latch logic.

## Function Blocks (FBs) Used

- **DigitalInput_I1**: logiBUS digital input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Reads the physical input `Input_I1` and provides it as the AX adapter output `IN`.
- **AX_RF_TRIG**: edge detection
    - **Type**: [`adapter::events::unidirectional::AX_RF_TRIG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_RF_TRIG/)
    - **Parameters**: none
    - **Explanation**: Evaluates the signal at the adapter `QI` and reports a rising edge (0→1) as event `ER` and a falling edge (1→0) as event `EF`.
- **AX_RS**: set/reset latch
    - **Type**: [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/)
    - **Parameters**: none
    - **Explanation**: The event `S` sets the output `Q` to TRUE, `R` sets it to FALSE. The latch holds the state between events.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Sets the physical output `Output_Q1` to the state of `AX_RS.Q`.

## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_RF_TRIG.QI` (AdapterConnection): The input signal is fed to the edge detection.
2. `AX_RF_TRIG.ER` → `AX_RS.S` (EventConnection): The rising edge sets the latch.
3. `AX_RF_TRIG.EF` → `AX_RS.R` (EventConnection): The falling edge resets the latch.
4. `AX_RS.Q` → `DigitalOutput_Q1.OUT` (AdapterConnection): The latch state is applied to the physical output `Q1`.

**Behavior**: When `I1` is pressed, the rising edge sets the latch and `Q1` turns on. When it is released, the falling edge resets the latch and `Q1` turns off.

## Application Example

This pattern is the basis for **signal filtering or debouncing**. If further logic (e.g. timers) is inserted between the edge detection and the latch, one can control very precisely under which conditions a signal should "latch in" or "drop out".

## Summary

The exercise shows how an input signal is applied to an output as a buffer via edge detection (`AX_RF_TRIG`) and a set/reset latch (`AX_SR`). The result corresponds to a direct pass-through, but the signal processing is event-based.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
