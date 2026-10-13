# Uebung_094b_AX: Select an Option Group by VT Input (Index)

![Uebung_094b_AX_network](./Uebung_094b_AX_network.svg)

* * * * * * * * * *

## Introduction

A VT input field (`InputNumber_I1`) selects one of two option groups by index: [`AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/) enables exactly one of the two `OptionalGroup` sub-apps via the [`AOption`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/AOption/) adapter (`QI` instead of Permit). The value reported back by the consumer is additionally written back to the input field (echo).

## Function Blocks (FBs) Used

- **NumericValue_IDA_1**: Read VT input field
    - **Type**: [`isobus::UT::io::NumericValue::NumericValue_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_IDA/)
    - **Parameters**: `QI = TRUE`, `u16ObjId = InputNumber_I1`
    - **Explanation**: Reads the value of the VT input field `InputNumber_I1` and outputs it via the `IN` adapter.
- **AD_TO_AUI_1**: Adapter converter AD to AUI
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AUI/AD_TO_AUI/)
    - **Parameters**: none
    - **Explanation**: Converts the `AD_IN` adapter of the input field into an `AUI_OUT`.
- **AUI_TO_AUI2**: Bidirectional bridge
    - **Type**: [`adapter::conversion::bidirectional::AUI_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/bidirectional/UINT/AUI_TO_AUI2/)
    - **Parameters**: none
    - **Explanation**: Provides the index as bidirectional plug `AUI2_BIDIR` for the consumer and delivers the value reported back by the consumer via `AUI_OUT`.
- **AOption_AUI_DEMUX_2**: Selects exactly one of two option groups
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/)
    - **Parameters**: none
    - **Explanation**: Receives the index at socket `K` and enables exactly one of the two option groups via `OUT1`/`OUT2`.
- **OptionalGroup_001**: Option group 1
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Enabled via the `OPTION` adapter (QI instead of Permit).
- **OptionalGroup_002**: Option group 2
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Enabled via the `OPTION` adapter (QI instead of Permit).
- **AUI_TO_AUDI_1**: Adapter converter AUI to AUDI
    - **Type**: [`adapter::conversion::unidirectional::AUI_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AUDI_AUI/AUI_TO_AUDI/)
    - **Parameters**: none
    - **Explanation**: Converts the reported index into an `AUDI_OUT` for the VT write-back.
- **Q_NumericValue_1**: Write VT value
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = InputNumber_I1`
    - **Explanation**: Writes the value back to the input field `InputNumber_I1` via `u32NewValue`.

## Program Flow and Connections

1. `NumericValue_IDA_1.IN` → `AD_TO_AUI_1.AD_IN` (AdapterConnection): the input field value is converted into an AUI stream.
2. `AD_TO_AUI_1.AUI_OUT` → `AUI_TO_AUI2.AUI_IN`: the value is fed into the bidirectional bridge.
3. `AUI_TO_AUI2.AUI2_BIDIR` → `AOption_AUI_DEMUX_2.K`: the index reaches the demultiplexer.
4. `AOption_AUI_DEMUX_2.OUT1` → `OptionalGroup_001.OPTION` and `AOption_AUI_DEMUX_2.OUT2` → `OptionalGroup_002.OPTION`: exactly one group is enabled.
5. `AUI_TO_AUI2.AUI_OUT` → `AUI_TO_AUDI_1.AUI_IN` → `Q_NumericValue_1.u32NewValue`: the reported value is written to the input field.

## Summary

The exercise shows how an option group is selected by VT input: the index runs via `AUI_TO_AUI2` into `AOption_AUI_DEMUX_2`, which enables exactly one of two option groups via `QI`; the feedback is mirrored into the input field.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
