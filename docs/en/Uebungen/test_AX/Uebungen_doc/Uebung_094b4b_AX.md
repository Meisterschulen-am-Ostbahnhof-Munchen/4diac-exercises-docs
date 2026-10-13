# Exercise_094b4b_AX: Option Selection (2 Options) with INI-Persisted Index

![Uebung_094b4b_AX_network](./Uebung_094b4b_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise corresponds to `Uebung_094b4_AX` but uses [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) instead of [`NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/): the bidirectional `VALUEO` plug goes directly to `AOption_AUI_DEMUX_2.K`, and the index is additionally persisted via INI. The constants `KEY_I1_STORE` and `SECTION_I1_STORE` are already known from `Uebung_012c_AUDI`.

## Function Blocks (FBs) Used

- **NumericValue_TO_AUI2_AND_STORE_1**: reads the VT input field, provides it as an AUI2 plug and stores it in INI
    - **Type**: [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) (SubApp instance)
    - **Parameters**: `KEY = KEY_I1_STORE`, `SECTION = SECTION_I1_STORE`, `u16ObjId = InputNumber_I1`
    - **Explanation**: Like `NumericValue_TO_AUI2`, with additional INI persistence: at boot the value loaded from the INI wins against the live VT reading, afterwards every live change.
- **AOption_AUI_DEMUX_2**: option selection with two outputs
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/)
    - **Parameters**: none
    - **Explanation**: Depending on the index `K`, activates exactly one of the outputs `OUT1` or `OUT2`.
- **OptionalGroup_001**, **OptionalGroup_002**: option groups
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) and [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp instances)
    - **Parameters**: none
    - **Explanation**: Group 1 contains `I1`/`Q1`, group 2 contains `I2`/`Q2`.

## Program Flow and Connections

1. `NumericValue_TO_AUI2_AND_STORE_1.VALUEO` → `AOption_AUI_DEMUX_2.K`: The index (possibly loaded from the INI) selects the active output.
2. `AOption_AUI_DEMUX_2.OUT1` → `OptionalGroup_001.OPTION`, `OUT2` → `OptionalGroup_002.OPTION`.

**Behavior**: Like `Uebung_094b4_AX` (index 1 = group 1, index 2 = group 2); in addition the selection survives a restart.

## Summary

Swapping in the `_AND_STORE` variant makes the option selection restart-proof without changing the rest of the network.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
