# Uebung_094b3d_AX: Option Selection with Two Sequentially Activated Option Groups via the T-Piece AOption_T_2

![Uebung_094b3d_AX_network](./Uebung_094b3d_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise corresponds to [`Uebung_094b3c_AX`](Uebung_094b3c_AX.md): the index `K = 2` activates two option groups ONE AFTER THE OTHER. Instead of a dedicated pass-through plug on the option group itself (`PLUG1` on `OptionalGroup_001a`), the new generic T-piece [`AOption_T_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/splitting/AOption_T_2/) is used here. `OptionalGroup_001` and `OptionalGroup_001b` are both the simple, unchanged variant with only one `OPTION` socket. The T-piece sits externally between the DEMUX and the two groups and can equally be passed on to a third group (or another T-piece). Activation is sequential: first `OUT2` (`OptionalGroup_001`), and only afterwards `OUT1` (`OptionalGroup_001b`), not simultaneously.

## Function Blocks (FBs) Used

- **NumericValue_TO_AUI2_1**: reads the VT input field and provides it as a bidirectional AUI2 plug
    - **Type**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp instance)
    - **Parameters**: `u16ObjId = InputNumber_I1`
    - **Explanation**: Reads the VT input field and passes the value on via `VALUEO` to the DEMUX.
- **AOption_AUI_DEMUX_3**: option selection with three outputs
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/)
    - **Parameters**: none
    - **Explanation**: Depending on the index `K`, activates exactly one of the outputs `OUT1`, `OUT2`, `OUT3`.
- **AOption_T_2**: option T-piece
    - **Type**: `Option::splitting::AOption_T_2`
    - **Parameters**: none
    - **Explanation**: Is fed at its `IN` socket from the DEMUX output `OUT2` and activates sequentially: first `OUT2` (`OptionalGroup_001`), and only after its `INIT_CNF` `OUT1` (`OptionalGroup_001b`). `OUT1` can be connected to the `IN` socket of another `AOption_T_2` to cascade the chain.
- **OptionalGroup_001**: first option group
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Enables `Input_I1`/`Output_Q1`; only one `OPTION` socket.
- **OptionalGroup_001b**: second option group
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001b`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001b/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Identical in construction to `OptionalGroup_001` (also `Input_I1`/`Output_Q1`); passes nothing on.
- **OptionalGroup_002**: further option group
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Group with `I2`/`Q2`; activated or deactivated via the option adapter.
- **DummyGroup**: neutral placeholder slot
    - **Type**: [`MyLib_AX_Option::sys::DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: No real I/O and no effect; state "nothing selected".

## Program Flow and Connections

1. `NumericValue_TO_AUI2_1.VALUEO` → `AOption_AUI_DEMUX_3.K`: The input field value selects the active output.
2. `AOption_AUI_DEMUX_3.OUT1` → `DummyGroup.OPTION`, `AOption_AUI_DEMUX_3.OUT2` → `AOption_T_2.IN`, `AOption_AUI_DEMUX_3.OUT3` → `OptionalGroup_002.OPTION`.
3. `AOption_T_2.OUT2` → `OptionalGroup_001.OPTION` and `AOption_T_2.OUT1` → `OptionalGroup_001b.OPTION`: The T-piece activates `OptionalGroup_001` first, then `OptionalGroup_001b`.

**Behavior**: `K = 1` = nothing selected (`DummyGroup`), `K = 2` = `OptionalGroup_001` followed by `OptionalGroup_001b` (sequentially via `AOption_T_2`), `K = 3` = `OptionalGroup_002`.

## Summary

Compared to `Uebung_094b3c_AX`, the special group `OptionalGroup_001a` with `PLUG1` is dropped. Instead, the generic, cascadable T-piece `AOption_T_2` distributes the activation externally and sequentially to two simple option groups.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
