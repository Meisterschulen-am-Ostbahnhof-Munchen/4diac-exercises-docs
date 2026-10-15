# Uebung_094c_AX: Two Independent VT Dropdowns Each Enable One Option Group

![Uebung_094c_AX_network](./Uebung_094c_AX_network.svg)

* * * * * * * * * *

## Introduction

Two independent VT dropdowns (`InputNumber_I1` and `InputNumber_I2`) each enable their own option group: `AD_TO_AUI` → [`AOption_AUI_DEMUX_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_1/) → [`OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/)/[`OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) via the [`AOption`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/AOption/) adapter and `QI`. There is **no** mutual exclusion between the two groups yet; that would need an upstream arbiter that derives a common K value from the two independent dropdown events (open question from Krauternter: Bunker_Klappen/Aufnahme_Rechts, "last activated wins" plus boot tiebreak). The network contains the chain twice, once per dropdown (duplicated inline).

## Function Blocks (FBs) Used

- **NumericValue_IDA**: Read VT dropdown 1
    - **Type**: [`isobus::UT::io::NumericValue::NumericValue_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_IDA/)
    - **Parameters**: `QI = TRUE`, `u16ObjId = InputNumber_I1`
    - **Explanation**: Reads the VT input field `InputNumber_I1`.
- **AD_TO_AUI**: Adapter converter AD to AUI
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AUI/AD_TO_AUI/)
    - **Parameters**: none
    - **Explanation**: Converts `AD_IN` into `AUI_OUT`.
- **AUI_TO_AUI2**: Bidirectional bridge
    - **Type**: [`adapter::conversion::bidirectional::AUI_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/bidirectional/UINT/AUI_TO_AUI2/)
    - **Parameters**: none
    - **Explanation**: Delivers the index as `AUI2_BIDIR` to the demultiplexer and outputs the reported value via `AUI_OUT`.
- **AOption_AUI_DEMUX_OptionalGroup_001**: Enable for option group 1
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_1/)
    - **Parameters**: none
    - **Explanation**: Enables option group 1 via `OUT1` (socket `K`).
- **OptionalGroup_001**: Option group 1
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Enabled via the `AOption_IN` adapter (QI instead of Permit).
- **AUI_TO_AUDI_1**: Adapter converter AUI to AUDI
    - **Type**: [`adapter::conversion::unidirectional::AUI_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AUDI_AUI/AUI_TO_AUDI/)
    - **Parameters**: none
    - **Explanation**: Converts the reported value for the VT write-back.
- **Q_NumericValue_1**: Write VT value
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = InputNumber_I1`
    - **Explanation**: Writes the value back to `InputNumber_I1`.
- **NumericValue_IDA_1**: Read VT dropdown 2
    - **Type**: [`isobus::UT::io::NumericValue::NumericValue_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_IDA/)
    - **Parameters**: `QI = TRUE`, `u16ObjId = InputNumber_I2`
    - **Explanation**: Reads the VT input field `InputNumber_I2`.
- **AD_TO_AUI_1**: Adapter converter AD to AUI
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AUI/AD_TO_AUI/)
    - **Parameters**: none
    - **Explanation**: Converts `AD_IN` into `AUI_OUT`.
- **AUI_TO_AUI3**: Bidirectional bridge
    - **Type**: [`adapter::conversion::bidirectional::AUI_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/bidirectional/UINT/AUI_TO_AUI2/)
    - **Parameters**: none
    - **Explanation**: Delivers the index as `AUI2_BIDIR` to the demultiplexer and outputs the reported value via `AUI_OUT`.
- **AOption_AUI_DEMUX_OptionalGroup_002**: Enable for option group 2
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_1/)
    - **Parameters**: none
    - **Explanation**: Enables option group 2 via `OUT1` (socket `K`).
- **OptionalGroup_002**: Option group 2
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (sub-app)
    - **Parameters**: none
    - **Explanation**: Enabled via the `AOption_IN` adapter (QI instead of Permit).
- **AUI_TO_AUDI**: Adapter converter AUI to AUDI
    - **Type**: [`adapter::conversion::unidirectional::AUI_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AUDI_AUI/AUI_TO_AUDI/)
    - **Parameters**: none
    - **Explanation**: Converts the reported value for the VT write-back.
- **Q_NumericValue**: Write VT value
    - **Type**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameters**: `u16ObjId = InputNumber_I2`
    - **Explanation**: Writes the value back to `InputNumber_I2`.

## Program Flow and Connections

1. `NumericValue_IDA.IN` → `AD_TO_AUI.AD_IN` → `AUI_TO_AUI2.AUI_IN`; `AUI_TO_AUI2.AUI2_BIDIR` → `AOption_AUI_DEMUX_OptionalGroup_001.K`; `AOption_AUI_DEMUX_OptionalGroup_001.OUT1` → `OptionalGroup_001.AOption_IN` (dropdown 1).
2. `NumericValue_IDA_1.IN` → `AD_TO_AUI_1.AD_IN` → `AUI_TO_AUI3.AUI_IN`; `AUI_TO_AUI3.AUI2_BIDIR` → `AOption_AUI_DEMUX_OptionalGroup_002.K`; `AOption_AUI_DEMUX_OptionalGroup_002.OUT1` → `OptionalGroup_002.AOption_IN` (dropdown 2).
3. Echo dropdown 1: `AUI_TO_AUI2.AUI_OUT` → `AUI_TO_AUDI_1.AUI_IN` → `Q_NumericValue_1.u32NewValue`.
4. Echo dropdown 2: `AUI_TO_AUI3.AUI_OUT` → `AUI_TO_AUDI.AUI_IN` → `Q_NumericValue.u32NewValue`.

## Summary

Each dropdown independently enables its own option group; mutual exclusion is deliberately still missing. The duplicated chain is moved into a reusable block in `Uebung_094c2_AX`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
