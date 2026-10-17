# Uebung_094b3e_AX: Option Selection with a Third Group behind 001 and 002 (T-Piece and OR)

![Uebung_094b3e_AX_network](./Uebung_094b3e_AX_network.svg)

* * * * * * * * * *

## Introduction

The exercise builds on [`Uebung_094b3_AX`](Uebung_094b3_AX.md) (`K = 1` dummy, `K = 2` group 001, `K = 3` group 002). In addition, a third group `OptionalGroup_001b` is activated as soon as `OptionalGroup_001` OR `OptionalGroup_002` is active, but not for `DummyGroup`. Since a DEMUX output can only be connected to one socket, an [`AOption_T_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/splitting/AOption_T_2/) sits behind each of `OUT2` and `OUT3`. Its second output feeds [`AOption_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/merging/AOption_OR_2/), whose `OUT` activates the third group.

## Function Blocks (FBs) Used

- **NumericValue_TO_AUI2_1**: reads the VT input field and provides it as a bidirectional AUI2 plug
    - **Type**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp instance)
    - **Parameters**: `u16ObjId = InputNumber_I1`
    - **Explanation**: Reads the VT input field and passes the value on via `VALUEO` to the DEMUX.
- **AOption_AUI_DEMUX_3**: option selection with three outputs
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/)
    - **Parameters**: none
    - **Explanation**: Depending on the index `K`, activates exactly one of the outputs `OUT1`, `OUT2`, `OUT3`.
- **AOption_T_2_1 / AOption_T_2_2**
    - **Type**: [`Option::splitting::AOption_T_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/splitting/AOption_T_2/)
    - **Parameters**: none
    - **Explanation**: Fed from `OUT2` resp. `OUT3` of the DEMUX; `OUT2` activates group 001 resp. 002, afterwards `OUT1` the OR input.
- **AOption_OR_2_1**
    - **Type**: [`Option::merging::AOption_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/merging/AOption_OR_2/)
    - **Parameters**: none
    - **Explanation**: `OUT` is active when `IN1` or `IN2` is active and activates the third group.
- **OptionalGroup_001**
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Enables `Input_I1`/`Output_Q1`.
- **OptionalGroup_002**
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Enables `Input_I2`/`Output_Q2`.
- **OptionalGroup_001b**
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001b`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001b/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Third group (`Input_I1`/`Output_Q1`, identical to 001); active for `K = 2` or `K = 3`.
- **DummyGroup**
    - **Type**: [`MyLib_AX_Option::sys::DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: No real I/O; state "nothing selected".

## Program Flow and Connections

1. `NumericValue_TO_AUI2_1.VALUEO` → `AOption_AUI_DEMUX_3.K`: the value of the input field selects the active output.
2. `OUT1` → `DummyGroup.AOption_IN`, `OUT2` → `AOption_T_2_1.IN`, `OUT3` → `AOption_T_2_2.IN`.
3. `AOption_T_2_1.OUT2` → `OptionalGroup_001.AOption_IN`, `AOption_T_2_2.OUT2` → `OptionalGroup_002.AOption_IN`.
4. `AOption_T_2_1.OUT1` → `AOption_OR_2_1.IN1`, `AOption_T_2_2.OUT1` → `AOption_OR_2_1.IN2`.
5. `AOption_OR_2_1.OUT` → `OptionalGroup_001b.AOption_IN`.

**Behavior**: `K = 1` = `DummyGroup` (the third group stays off), `K = 2` = `OptionalGroup_001` and `OptionalGroup_001b`, `K = 3` = `OptionalGroup_002` and `OptionalGroup_001b`.

**Note**: like `OptionalGroup_001`, `OptionalGroup_001b` uses the I/O `Input_I1`/`Output_Q1`. The exercise is a wiring test and has not yet been verified on the device.

## Summary

A DEMUX output feeds only one socket. Therefore two `AOption_T_2` distribute the activation to the groups and to the OR, and `AOption_OR_2` activates the shared third group for `K = 2` or `K = 3`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
