# Exercise_103c: DigitalInput_I1 to DigitalOutput_Q1, with Plug and Socket and DEMUX

![Uebung_103c_network](./Uebung_103c_network.svg)

* * * * * * * * * *

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/041f4df4-b729-484d-b786-b6dcdf151961)

## Introduction

The goal of this exercise is to test a specific path of the MUX/DEMUX structure. Compared to [Uebung_103](./Uebung_103.md), the VT input field has been removed: the selection index for `AX_DEMUX_3` and `AX_MUX_3` is instead given by an `initval_AUI` with a fixed value. The button `I1` therefore permanently acts on the output `Q1` through the same one of the three branches (`tastend`, `rastend`, `tastend_TON_5s`), although the structure for the other modes is still present. This is often used for debugging or to quickly freeze a configuration.

## Function Blocks (FBs) Used

- **DigitalInput_I1**: logiBUS digital input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Provides the state of `I1` as an AX adapter signal.
- **initval_AUI**: fixed selection index
    - **Type**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameters**: `INIT_VAL = 0`
    - **Explanation**: Provides the constant index 0 as an AUI signal.
- **C2**: index distributor
    - **Type**: [`adapter::events::unidirectional::AUI_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: Distributes the index to `AX_AUI_MUX_3.K` (`OUT1`) and `AX_AUI_DEMUX_3.K` (`OUT2`).
- **AX_DEMUX_3**: demultiplexer
    - **Type**: [`adapter::selection::unidirectional::AX_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/selection/unidirectional/BOOL/AX_AUI_DEMUX_3/)
    - **Parameters**: none
    - **Explanation**: Forwards the signal from `IN` to one of the outputs `OUT1` to `OUT3`; the index arrives via the adapter `K` and is 0-based (index 0 → `OUT1`, 1 → `OUT2`, 2 → `OUT3`).
- **tastend**: branch 1, momentary function
    - **Type**: `MyLib::sys::tastend` (SubApp instance, see [tastend](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/tastend/))
    - **Parameters**: none
    - **Explanation**: Passes the signal directly (1:1) from `IN` to `OUT`.
- **rastend**: branch 2, latching function
    - **Type**: `MyLib::sys::rastend` (SubApp instance, see [rastend](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/rastend/))
    - **Parameters**: none
    - **Explanation**: Converts the button into a switch (toggle); toggling occurs on release (falling edge via `AX_F_TRIG` onto `AX_T_FF`).
- **tastend_TON_5s**: branch 3, momentary with 5 s on-delay
    - **Type**: `MyLib::sys::tastend_TON_5s` (SubApp instance, see [tastend_TON_5s](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/tastend_TON_5s/))
    - **Parameters**: none
    - **Explanation**: Passes the signal through an `AX_TON` with `PT = t#5s`, i.e. with a 5 s on-delay.
- **AX_MUX_3**: multiplexer
    - **Type**: [`adapter::selection::unidirectional::AX_AUI_MUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/selection/unidirectional/BOOL/AX_AUI_MUX_3/)
    - **Parameters**: none
    - **Explanation**: Passes the input `IN1` to `IN3` selected by the index `K` on to `OUT` (index 0 → `IN1`, 1 → `IN2`, 2 → `IN3`).
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs the result at the physical output `Q1`.

## Program Flow and Connections

1. `initval_AUI.OUT` → `C2.IN`; `C2.OUT2` → `AX_DEMUX_3.K` and `C2.OUT1` → `AX_MUX_3.K`: Both selectors receive the same fixed index.
2. `DigitalInput_I1.IN` → `AX_DEMUX_3.IN`: The button feeds the demultiplexer.
3. `AX_DEMUX_3.OUT1` → `tastend.IN`, `AX_DEMUX_3.OUT2` → `rastend.IN`, `AX_DEMUX_3.OUT3` → `tastend_TON_5s.IN`: The three branches.
4. `tastend.OUT` → `AX_MUX_3.IN1`, `rastend.OUT` → `AX_MUX_3.IN2`, `tastend_TON_5s.OUT` → `AX_MUX_3.IN3`: The branches converge in the multiplexer.
5. `AX_MUX_3.OUT` → `DigitalOutput_Q1.OUT`: The result of the selected branch is applied to `Q1`.

**Behavior**: With `INIT_VAL = 0` index 0 is selected, i.e. the branch `tastend`: `Q1` follows `I1` directly. If the value of `INIT_VAL` is changed to 1 or 2 in the source, the branch `rastend` or `tastend_TON_5s` takes effect instead.

## Application Example

Fixed configuration of a switchable operating logic, for debugging a single branch or to freeze a setting permanently.

## Summary

The exercise `Uebung_103c` shows the MUX/DEMUX structure with a fixed selection index: an `initval_AUI` applies the same index to `AX_AUI_DEMUX_3` and `AX_AUI_MUX_3` via `AUI_SPLIT_2`, so that only one of the three processing branches is active.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
