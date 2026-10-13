# Exercise_039a: Mirror Sequence V2 with Step Chain

![Uebung_039a_network](./Uebung_039a_network.svg)

* * * * * * * * * *

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/a6872e59-1dfc-4132-a118-aff1bc7bc944)

## Introduction

This exercise is a specialization of valve control for systems with 3/2-way valves (e.g. hydraulic ring systems like those used by Claas). The step chain `sequence_ET_05` manages the sequence of cylinder movements. The physical outputs `Q1` and `Q2` are controlled via one instance each of the block `Softkey_T_FF_ILOCK_TO_QX_BG`, which also provides direct visual feedback on the ISOBUS softkey. The current step number is shown on a VT numeric field. This demonstrates the adaptability of the logiBUS sequence blocks to various hydraulic wiring concepts.

## Function Blocks (FBs) Used

- **SoftKey_UP_F1**: softkey input for the start
    - **Type**: [`isobus::UT::io::Softkey::Softkey_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IE/)
    - **Parameters**: `QI = TRUE`, `u16ObjId = SoftKey_F1`, `InputEvent = SK_RELEASED`
    - **Explanation**: Reports the release of the softkey `SoftKey_F1` as event `IND`.
- **DigitalInput_DOWN_I1** ... **DigitalInput_DOWN_I4**: click inputs for the manual step transitions
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1` or `Input_I2`, `Input_I3`, `Input_I4`, each with `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Report a single click on `I1` to `I4` as event `IND`.
- **sequence_05**: step chain
    - **Type**: [`logiBUS::utils::sequence::combi::sequence_ET_05`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/utils/sequence/combi/sequence_ET_05/)
    - **Parameters**: `DT_S1_S2 = NO_TIME`, `DT_S2_S3 = NO_TIME`, `DT_S3_S4 = T#5s`, `DT_S4_S5 = NO_TIME`, `DT_S5_START = NO_TIME`
    - **Explanation**: Five steps with a start state. Each transition is event-driven or time-driven; `NO_TIME` disables the time control. Here only the transition from step 3 to step 4 runs automatically after 5 s, all other transitions are manual. On entering step n the block delivers the event `EO_Sn`, and on every change `CNF` with the step number `STATE_NR`.
- **E_TimeOut**: timer of the step chain
    - **Type**: [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/)
    - **Parameters**: none
    - **Explanation**: Provides, via the adapter `TimeOutSocket`, the time monitoring for the time-driven transitions of `sequence_05`.
- **Q1**: output `Q1` with softkey feedback
    - **Type**: [`MyLib::sys::Softkey_T_FF_ILOCK_TO_QX_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_B/sys/Softkey_T_FF_ILOCK_TO_QX_BG/) (SubApp instance)
    - **Parameters**: `u16ObjId = SoftKey_F8`, `Output = Output_Q1`
    - **Explanation**: Digital output as a toggle flip-flop: releasing the softkey `SoftKey_F8` toggles the output; in addition, the events `SET` and `RESET` can set or reset the output externally (interlock). The background colour of the softkey mirrors the state.
- **Q2**: output `Q2` with softkey feedback
    - **Type**: [`MyLib::sys::Softkey_T_FF_ILOCK_TO_QX_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_B/sys/Softkey_T_FF_ILOCK_TO_QX_BG/) (SubApp instance)
    - **Parameters**: `u16ObjId = SoftKey_F2`, `Output = Output_Q2`
    - **Explanation**: Like `Q1`, but for the softkey `SoftKey_F2` and the output `Q2`.
- **NumbAnzeig**: display of the step number
    - **Type**: [`MyLib::sys::SINT_TO_Q_NumericValue`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_B/sys/SINT_TO_Q_NumericValue/) (SubApp instance)
    - **Parameters**: `u16ObjId = OutputNumber_N1`
    - **Explanation**: Converts the SINT value `NewValue` to UINT and shows it on the VT numeric field `OutputNumber_N1`.

## Program Flow and Connections

1. `SoftKey_UP_F1.IND` → `sequence_05.START_S1` (EventConnection): Releasing `SoftKey_F1` starts the step chain in step 1.
2. `DigitalInput_DOWN_I1.IND` → `sequence_05.S1_S2`, `DigitalInput_DOWN_I2.IND` → `sequence_05.S2_S3`, `DigitalInput_DOWN_I3.IND` → `sequence_05.S4_S5` and `DigitalInput_DOWN_I4.IND` → `sequence_05.S5_START`: A click on `I1`, `I2`, `I3` or `I4` manually advances from step 1 to 2, from 2 to 3, from 4 to 5, or from 5 back to the start. The transition from step 3 to 4 is not wired and runs time-driven after `DT_S3_S4 = T#5s`.
3. `sequence_05.timeOut` → `E_TimeOut.TimeOutSocket` (AdapterConnection): The time control of the step chain.
4. `sequence_05.EO_S1` → `Q1.SET`: In step 1, `Q1` is set.
5. `sequence_05.EO_S2` → `Q2.SET`: In step 2, `Q2` is set.
6. `sequence_05.EO_S4` → `Q2.RESET`: In step 4, `Q2` is reset.
7. `sequence_05.EO_S5` → `Q1.RESET`: In step 5, `Q1` is reset.
8. `sequence_05.CNF` → `NumbAnzeig.CNF` (EventConnection) and `sequence_05.STATE_NR` → `NumbAnzeig.NewValue` (DataConnection): On every step change the step number is shown on the VT.

**Behavior (mirror sequence)**: `Q1` is set in step 1 and only reset in step 5; `Q2` is set in step 2 and reset in step 4. The outputs are therefore switched on and off in mirrored order.

## Summary

The exercise shows how a five-step chain (`sequence_ET_05`) sets and resets two outputs in a mirror sequence, with transitions partly manual via click inputs and partly time-driven. The output blocks give visual feedback on the softkeys, and the current step number is shown on the VT.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
