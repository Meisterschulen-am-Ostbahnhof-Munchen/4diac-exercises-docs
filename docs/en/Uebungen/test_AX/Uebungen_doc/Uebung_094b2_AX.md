# Exercise_094b2_AX: Option Selection via VT Input Field with Explicit "Nothing Selected" State

![Uebung_094b2_AX_network](./Uebung_094b2_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise corresponds to `Uebung_094b_AX` but uses [`AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/) instead of [`AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/). The value `K = 1` goes to a neutral [`DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) without any effect, `K = 2` and `K = 3` activate the real groups [`OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) and [`OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/). This removes the question of which numeric value should mean "off": a dedicated placeholder slot is simply reserved in the DEMUX. The read/echo chain to the VT input field is still wired inline here.

## Function Blocks (FBs) Used

- **NumericValue_IDA_1**: reads the VT input field
    - **Type**: `isobus::UT::io::NumericValue::NumericValue_IDA`
    - **Parameters**: `QI = TRUE`, `u16ObjId = InputNumber_I1`
    - **Explanation**: Provides the value of the VT number field `InputNumber_I1` via the adapter `IN` (AD).
- **AD_TO_AUI_1**: adapter conversion AD to AUI
    - **Type**: `adapter::conversion::unidirectional::AD_TO_AUI`
    - **Parameters**: none
    - **Explanation**: Converts the DWORD value into a UINT value (AUI).
- **AUI_TO_AUI2**: bridge to the bidirectional AUI2 adapter
    - **Type**: `adapter::conversion::bidirectional::AUI_TO_AUI2`
    - **Parameters**: none
    - **Explanation**: Passes the value via `AUI2_BIDIR` to the consumer (`K` of the DEMUX) and receives the value actually accepted there back as an echo (`AUI_OUT`).
- **AUI_TO_AUDI_1**: adapter conversion AUI to AUDI
    - **Type**: `adapter::conversion::unidirectional::AUI_TO_AUDI`
    - **Parameters**: none
    - **Explanation**: Converts the echo back into a UDINT value for the VT.
- **Q_NumericValue_1**: writes the value to the VT
    - **Type**: `isobus::UT::Q::Q_NumericValue_AUDI`
    - **Parameters**: `u16ObjId = InputNumber_I1`
    - **Explanation**: Writes the returned value (`u32NewValue`) into the VT field `InputNumber_I1`.
- **AOption_AUI_DEMUX_3**: option selection with three outputs
    - **Type**: [`Option::selection::AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/)
    - **Parameters**: none
    - **Explanation**: Depending on the index `K`, activates exactly one of the outputs `OUT1`, `OUT2`, `OUT3`.
- **OptionalGroup_001**, **OptionalGroup_002**: real option groups
    - **Type**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) and [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp instances)
    - **Parameters**: none
    - **Explanation**: Group 1 contains the digital input `I1` and digital output `Q1`, group 2 contains `I2` and `Q2`. The whole group is enabled or disabled via the option adapter.
- **DummyGroup**: neutral placeholder slot
    - **Type**: [`MyLib_AX_Option::sys::DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) (SubApp instance)
    - **Parameters**: none
    - **Explanation**: Has no real I/O and no effect; it acknowledges initialization itself. Serves as the "nothing selected" state.

## Program Flow and Connections

1. `NumericValue_IDA_1.IN` → `AD_TO_AUI_1.AD_IN` → `AUI_TO_AUI2.AUI_IN`: The input field value is converted to AUI and then into the bidirectional AUI2 adapter.
2. `AUI_TO_AUI2.AUI2_BIDIR` → `AOption_AUI_DEMUX_3.K`: The value selects the active output of the DEMUX.
3. `AOption_AUI_DEMUX_3.OUT1` → `DummyGroup.OPTION`, `OUT2` → `OptionalGroup_001.OPTION`, `OUT3` → `OptionalGroup_002.OPTION`.
4. Echo path: `AUI_TO_AUI2.AUI_OUT` → `AUI_TO_AUDI_1.AUI_IN` → `Q_NumericValue_1.u32NewValue`: The value accepted by the DEMUX is written back into the VT field.

**Behavior**: In the input field `InputNumber_I1`, 1 gives the "nothing selected" state (DummyGroup), 2 activates group 1 and 3 activates group 2.

## Summary

The additional placeholder slot in `AOption_AUI_DEMUX_3` provides an unambiguous "nothing selected" state without any special numeric convention. The inline-wired read/echo chain is moved into a reusable block in `Uebung_094b3_AX`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
