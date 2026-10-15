# Exercise_094b5_AX: Option Selection via Bitmask (Multi-Select) with AOption_AD_DEMUX_2

![Uebung_094b5_AX_network](./Uebung_094b5_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise corresponds to `Uebung_094b_AX` but uses [`AOption_AD_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AD_DEMUX_2/) (bitmask, multi-select) instead of [`AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/) (index, single select): bit 0 switches [`OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/), bit 1 switches [`OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/); both can be active at the same time. Here the bitmask is assembled from two VT checkboxes, and the state is written back into the checkboxes. `AD_TO_AUI` and `AUI_TO_AUI2` are replaced by [`AD_TO_AD2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/bidirectional/DWORD/AD_TO_AD2/), since the value is already available as DWORD (AD).

## Function Blocks (FBs) Used

- **Checkbox_IDA_TO_AX**, **Checkbox_IDA_TO_AX_1**: read a VT checkbox
    - **Type**: `MyLib::sys::Checkbox_IDA_TO_AX` (SubApp instances)
    - **Parameters**: none set (`u16ObjId` stays at its default `ID_NULL`)
    - **Explanation**: Passes the on/off state of a VT checkbox as an AX level via `PERMIT_OUT`. The object ID (`u16ObjId`, the ObjectID of the NumberVariable referenced by the checkbox) still has to be set for real operation.
- **ASSEMBLE_AD_FROM_AX**: assemble the bitmask
    - **Type**: `adapter::assembling::ASSEMBLE_AD_FROM_AX`
    - **Parameters**: none
    - **Explanation**: Assembles a DWORD value (AD) from the AX inputs `BIT_00` and `BIT_01`.
- **AD_TO_AD2**: bridge to the bidirectional AD2 adapter
    - **Type**: [`adapter::conversion::bidirectional::AD_TO_AD2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/bidirectional/DWORD/AD_TO_AD2/)
    - **Parameters**: none
    - **Explanation**: Passes the bitmask via `AD2_BIDIR` to the consumer (`K` of the DEMUX) and returns the value accepted by the consumer via `AD_OUT`.
- **AOption_AD_DEMUX_2**: option selection via bitmask
    - **Type**: [`Option::selection::AOption_AD_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AD_DEMUX_2/)
    - **Parameters**: none
    - **Explanation**: Bit 0 of `K` activates `OUT1`, bit 1 activates `OUT2`; several outputs can be active at the same time.
- **OptionalGroup_001**, **OptionalGroup_002**: option groups
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) and [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp instances)
    - **Parameters**: none
    - **Explanation**: Group 1 contains `I1`/`Q1`, group 2 contains `I2`/`Q2`.
- **SPLIT_AD_INTO_AX**: split the bitmask
    - **Type**: `adapter::splitting::SPLIT_AD_INTO_AX`
    - **Parameters**: none
    - **Explanation**: Splits the returned DWORD value into the AX levels `BIT_00` and `BIT_01`.
- **AX_TO_Checkbox**, **AX_TO_Checkbox_1**: write a VT checkbox
    - **Type**: [`MyLib::sys::AX_TO_Checkbox`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/AX_TO_Checkbox/) (SubApp instances)
    - **Parameters**: none set (`u16ObjId` stays at its default `ID_NULL`)
    - **Explanation**: Counterpart of `Checkbox_IDA_TO_AX`: writes the AX level of `AX_IN` to the VT checkbox on every change.

## Program Flow and Connections

1. `Checkbox_IDA_TO_AX.PERMIT_OUT` → `ASSEMBLE_AD_FROM_AX.BIT_00` and `Checkbox_IDA_TO_AX_1.PERMIT_OUT` → `ASSEMBLE_AD_FROM_AX.BIT_01`: The two checkboxes form bit 0 and bit 1 of the mask.
2. `ASSEMBLE_AD_FROM_AX.OUT` → `AD_TO_AD2.AD_IN`, `AD_TO_AD2.AD2_BIDIR` → `AOption_AD_DEMUX_2.K`: The mask selects the active outputs.
3. `AOption_AD_DEMUX_2.OUT1` → `OptionalGroup_001.AOption_IN`, `OUT2` → `OptionalGroup_002.AOption_IN`.
4. Echo path: `AD_TO_AD2.AD_OUT` → `SPLIT_AD_INTO_AX.IN`; `BIT_00` → `AX_TO_Checkbox.AX_IN`, `BIT_01` → `AX_TO_Checkbox_1.AX_IN`: The accepted value is written back into the checkboxes.

**Behavior**: Checkbox 1 switches group 1, checkbox 2 switches group 2; both groups can be active at the same time.

## Summary

With `AOption_AD_DEMUX_2`, the single selection of the previous exercises becomes a multi-select via bitmask. `ASSEMBLE_AD_FROM_AX` and `SPLIT_AD_INTO_AX` connect the individual checkbox levels to the bitmask and back.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
