# Exercise_039b_AX: Mirror Sequence V2 with Step Chain

![Uebung_039b_AX_network](./Uebung_039b_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements a mirror sequence (V2) as a step chain of adapter-based timing elements: pressing the softkey `SoftKey_F1` starts an 8-second pulse on `Q1`. Along with the pulse, a 2-second on-delay starts; when it expires, a 4-second pulse is triggered on `Q2`.

## Function Blocks (FBs) Used

- **SoftKey_F1**: Softkey input of the ISOBUS terminal
    - **Type**: [`isobus::UT::io::Softkey::Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/)
    - **Parameters**: `QI = TRUE`, `u16ObjId = SoftKey_F1`
    - **Explanation**: Provides the state of the softkey `SoftKey_F1` as AX adapter output `IN`.
- **E_TP_Q1**: Pulse generator for `Q1`
    - **Type**: [`adapter::events::unidirectional::timers::AX_TP`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/timers/AX_TP/)
    - **Parameters**: `PT = T#8s`
    - **Explanation**: Generates a pulse of 8 seconds length at the adapter output `Q` from the button signal.
- **AX_SPLIT_2**: Adapter signal distributor
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: Distributes the pulse from `E_TP_Q1.Q` to two targets: `OUT1` (output `Q1`) and `OUT2` (on-delay).
- **E_TON**: On-delay
    - **Type**: [`adapter::events::unidirectional::timers::AX_TON`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/timers/AX_TON/)
    - **Parameters**: `PT = T#2s`
    - **Explanation**: Delays switching on the signal from `AX_SPLIT_2.OUT2` by 2 seconds.
- **E_TP_Q2**: Pulse generator for `Q2`
    - **Type**: [`adapter::events::unidirectional::timers::AX_TP`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/timers/AX_TP/)
    - **Parameters**: `PT = T#4s`
    - **Explanation**: Generates a pulse of 4 seconds length from the delayed signal of `E_TON.Q`.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Switches the physical output `Q1` according to the 8-second pulse.
- **DigitalOutput_Q2**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q2`
    - **Explanation**: Switches the physical output `Q2` according to the 4-second pulse.

## Program Flow and Connections

1. `SoftKey_F1.IN` → `E_TP_Q1.IN` (AdapterConnection): The softkey starts the 8-second pulse.
2. `E_TP_Q1.Q` → `AX_SPLIT_2.IN` (AdapterConnection): The pulse is distributed to two targets.
3. `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT` (AdapterConnection): `Q1` follows the 8-second pulse.
4. `AX_SPLIT_2.OUT2` → `E_TON.IN` (AdapterConnection): The pulse starts the 2-second on-delay.
5. `E_TON.Q` → `E_TP_Q2.IN` (AdapterConnection): The delayed signal starts the 4-second pulse.
6. `E_TP_Q2.Q` → `DigitalOutput_Q2.OUT` (AdapterConnection): `Q2` follows the 4-second pulse.

## Summary

Exercise 039b_AX forms a time-controlled step chain from the blocks `AX_TP` and `AX_TON`: a key press triggers `Q1` for 8 seconds and, via the 2-second on-delay, starts a 4-second pulse on `Q2`. `AX_SPLIT_2` distributes the pulse to the output and the following step without additional event connections.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
