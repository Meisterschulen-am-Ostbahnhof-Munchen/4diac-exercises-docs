# Uebung_094b3f_AX: Option Selection with a Third Group via OptionalGroup_001a/002a and OR

![Uebung_094b3f_AX_network](./Uebung_094b3f_AX_network.svg)

* * * * * * * * * *

## Introduction

The exercise corresponds to [`Uebung_094b3e_AX`](Uebung_094b3e_AX.md) but needs no T-pieces: groups 1 and 2 are [`OptionalGroup_001a`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001a/) and [`OptionalGroup_002a`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002a/) with `AOption_IN` and `AOption_OUT`. Their `AOption_OUT` go to [`AOption_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/merging/AOption_OR_2/), whose `OUT` activates the third group `OptionalGroup_001b`. `DummyGroup` is not connected to the OR.

## Function Blocks (FBs) Used

- **NumericValue_TO_AUI2_1**: reads the VT input field and provides it as a bidirectional AUI2 plug
    - **Type**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp instance)
    - **Parameters**: `u16ObjId = InputNumber_I1`
    - **Explanation**: Reads the VT input field and passes the value on via `VALUEO` to the DEMUX.
- **AOption_AUI_DEMUX_3**: option selection with three outputs
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/)
    - **Parameters**: none
    - **Explanation**: Depending on the index `K`, activates exactly one of the outputs `OUT1`, `OUT2`, `OUT3`.
- **OptionalGroup_001a**
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001a`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001a/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Group 1 (`Input_I1`/`Output_Q1`) with `AOption_IN` and `AOption_OUT`.
- **OptionalGroup_002a**
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_002a`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002a/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Group 2 (`Input_I2`/`Output_Q2`) with `AOption_IN` and `AOption_OUT`.
- **AOption_OR_2_1**
    - **Type**: [`Option::merging::AOption_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/merging/AOption_OR_2/)
    - **Parameters**: none
    - **Explanation**: `OUT` is active when `IN1` or `IN2` is active.
- **OptionalGroup_001b**
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001b`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001b/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Third group (`Input_I1`/`Output_Q1`), last element.
- **DummyGroup**
    - **Type**: [`MyLib_AX_Option::sys::DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: No real I/O; state "nothing selected".

## Program Flow and Connections

1. `NumericValue_TO_AUI2_1.VALUEO` → `AOption_AUI_DEMUX_3.K`.
2. `OUT1` → `DummyGroup.AOption_IN`, `OUT2` → `OptionalGroup_001a.AOption_IN`, `OUT3` → `OptionalGroup_002a.AOption_IN`.
3. `OptionalGroup_001a.AOption_OUT` → `AOption_OR_2_1.IN1`, `OptionalGroup_002a.AOption_OUT` → `AOption_OR_2_1.IN2`.
4. `AOption_OR_2_1.OUT` → `OptionalGroup_001b.AOption_IN`.

**Behavior**: `K = 1` = `DummyGroup` (third group off), `K = 2` = `OptionalGroup_001a` and then `OptionalGroup_001b`, `K = 3` = `OptionalGroup_002a` and then `OptionalGroup_001b`. The `a` groups only report `INIT_CNF` back once the downstream chain has confirmed.

**Note**: `OptionalGroup_001b` uses the same I/O as `OptionalGroup_001a`. The exercise is a wiring test and has not yet been verified on the device.

## Summary

The groups pass the activation on themselves via `AOption_OUT`, so no T-pieces are needed. `AOption_OR_2` activates the third group as soon as one of the two is active.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
