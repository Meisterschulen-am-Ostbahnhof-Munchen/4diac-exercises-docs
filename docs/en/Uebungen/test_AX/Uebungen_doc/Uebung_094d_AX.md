# Uebung_094d_AX: Option Group Without DEMUX – Always Active, Initialized Once at Resource Start

![Uebung_094d_AX_network](./Uebung_094d_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise shows [`OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) WITHOUT a DEMUX: the group is always active and no index `K` is needed. Instead of an `AOption_AUI_DEMUX_N` selecting one of several `OUTx` outputs depending on `K`, the resource start triggers `INIT_REQ` once with a constant `QI_OUT = TRUE` (`E_RESTART` and `E_TRIG` live in the `EMB_RES`, not in the SubApp). The block [`AOption_EVENT_TO_X`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/conversion/AOption_EVENT_TO_X/) holds the plug `AOption_OUT` of the [`AOption`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/AOption/) adapter.

## Function Blocks (FBs) Used

- **AOption_EVENT_TO_X**: converts an event with qualifier into an option adapter
    - **Type**: [`Option::conversion::AOption_EVENT_TO_X`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/conversion/AOption_EVENT_TO_X/)
    - **Parameters**: `QI_OUT = TRUE`
    - **Explanation**: Composite FB with the plug `AOption_OUT`. `INIT_REQ` and `QI_OUT` are passed on to the adapter, and `INIT_CNF` of the adapter is output as `INIT_CNF`. `QI_OUT = TRUE` requests or activates the option.
- **OptionalGroup_001**: option group 1
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Enables `Input_I1`/`Output_Q1` via the option adapter (socket `AOption_IN`); `QI` activates or deactivates the whole group.

## Program Flow and Connections

1. In the `EMB_RES`, `E_RESTART` (`START.COLD`) feeds an `E_TRIG` (`EVENTTYPE = 'EInit'`) that triggers the initialization once, arriving as `AOption_EVENT_TO_X.INIT_REQ`; the parameter `QI_OUT = TRUE` is set constantly.
2. `AOption_EVENT_TO_X.AOption_OUT` → `OptionalGroup_001.AOption_IN` (AdapterConnection): The adapter passes `INIT_REQ` and `QI_OUT = TRUE` on to the group.
3. The group initializes its I/O blocks with `QI = TRUE` and is thus permanently active.

**Behavior**: There is no DEMUX and no index `K`. The group is initialized once after start and stays active.

## Learning Objectives

- Operating an option group even without a selection DEMUX
- Feeding the option adapter `AOption` directly from an event with [`AOption_EVENT_TO_X`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/conversion/AOption_EVENT_TO_X/)
- Triggering the initialization once via `E_RESTART` and `E_TRIG` in the `EMB_RES`
- Setting the qualifier `QI_OUT` as a constant

## Summary

Compared to the exercises with `AOption_AUI_DEMUX_N`, index and selection are dropped: `AOption_EVENT_TO_X` with `QI_OUT = TRUE` feeds the group `OptionalGroup_001` directly, so that it is always active.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
