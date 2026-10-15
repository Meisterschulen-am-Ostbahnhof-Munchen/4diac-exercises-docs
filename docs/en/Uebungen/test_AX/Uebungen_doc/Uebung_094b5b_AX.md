# Uebung_094b5b_AX: Checkbox Chain as a Reusable Block

![Uebung_094b5b_AX_network](./Uebung_094b5b_AX_network.svg)

* * * * * * * * * *

## Introduction

Second version of `Uebung_094b5_AX`: everything except [`AOption_AD_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AD_DEMUX_2/), [`OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) and [`OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) has been moved into the reusable block [`MyLib::sys::Checkbox_TO_AD2_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Checkbox_TO_AD2_2/). The network therefore consists of only four instances.

## Function Blocks (FBs) Used

- **Checkbox_TO_AD2_2**: Reusable block for two VT checkboxes
    - **Type**: [`MyLib::sys::Checkbox_TO_AD2_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Checkbox_TO_AD2_2/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Combines two VT checkboxes into a bit mask and provides it at the bidirectional plug `K`. Replaces the individually wired chain `Checkbox_IDA_TO_AX`/`ASSEMBLE_AD_FROM_AX`/[`AD_TO_AD2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/bidirectional/DWORD/AD_TO_AD2/)/`SPLIT_AD_INTO_AX`/[`AX_TO_Checkbox`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/AX_TO_Checkbox/) from `Uebung_094b5_AX`.
- **AOption_AD_DEMUX_2**: Selection of option groups
    - **Type**: [`Option::selection::AOption_AD_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AD_DEMUX_2/)
    - **Parameters**: none
    - **Explanation**: Receives the bit mask at socket `K` and enables the option groups via `OUT1`/`OUT2`.
- **OptionalGroup_001**: Option group 1
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Enabled via the `AOption_IN` adapter (QI instead of Permit).
- **OptionalGroup_002**: Option group 2
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Enabled via the `AOption_IN` adapter (QI instead of Permit).

## Program Flow and Connections

1. `Checkbox_TO_AD2_2.K` → `AOption_AD_DEMUX_2.K` (AdapterConnection): the bit mask of the two checkboxes reaches the demultiplexer.
2. `AOption_AD_DEMUX_2.OUT1` → `OptionalGroup_001.AOption_IN`: enables option group 1.
3. `AOption_AD_DEMUX_2.OUT2` → `OptionalGroup_002.AOption_IN`: enables option group 2.

## Summary

Functionally the exercise corresponds to `Uebung_094b5_AX`, but moving the chain into [`Checkbox_TO_AD2_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Checkbox_TO_AD2_2/) makes it much clearer.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
