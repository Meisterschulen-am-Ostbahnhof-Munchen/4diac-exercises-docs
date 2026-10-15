# Uebung_006_SAFE_AX_02: Start/Stop with SF_EmergencyStop instead of StartPermit/StartGate

![Uebung_006_SAFE_AX_02_network](./Uebung_006_SAFE_AX_02_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the second SAFE variant of `Uebung_006_AX`. Instead of the hand-built solution of `StartPermit` (E_RS) and `StartGate` (E_PERMIT) in `Uebung_006_SAFE_AX`, it uses the PLCopen standard block `SF_EmergencyStop` (Part 1, Ch. 6.5). `SF_EmergencyStop` and `SF_ResetButton` are coupled in the embedded pattern (Figure 13 of the specification) via `ResetRequest`/`ResetRequested`: `SF_EmergencyStop` only asks `SF_ResetButton` to monitor when a reset is actually needed. With `S_StartReset = FALSE` and `S_AutoReset = FALSE` there is no automatic restart; the RESET button (`I8`) is always mandatory.

## Function Blocks (FBs) Used

- **START**: START button
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I1` as event `IND`.
- **STOP**: Manual stop (not the hardware emergency stop)
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I2` as event `IND`.
- **EStopFeedback**: Feedback of the emergency stop circuit (`I7 = TRUE`: emergency stop circuit ok, `I7 = FALSE`: stop detected)
    - **Type**: [`logiBUS::io::DI::logiBUS_IX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IX/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I7`
    - **Explanation**: Provides the level `IN` and, on a change, the event `IND`.
- **ResetInput**: Level and event for `SF_ResetButton`
    - **Type**: [`logiBUS::io::DI::logiBUS_IX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IX/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I8`
    - **Explanation**: Provides the level of the RESET button `I8`.
- **ResetButton**: PLCopen reset button monitoring
    - **Type**: `logiBUS::safety::PLCopen::SF_ResetButton`
    - **Parameters**: `TrailingMinimum = T#350ms`, `TrailingMaximum = T#2s`
    - **Explanation**: Evaluates the reset button using the time limits `TrailingMinimum` and `TrailingMaximum`; the time monitoring runs via the adapters `minTimeOut`/`maxTimeOut`.
- **EmergencyStop**: PLCopen emergency stop
    - **Type**: [`logiBUS::safety::PLCopen::SF_EmergencyStop`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/safety/PLCopen/SF_EmergencyStop/)
    - **Parameters**: `Activate = TRUE`, `S_StartReset = FALSE`, `S_AutoReset = FALSE`
    - **Explanation**: Evaluates the emergency stop feedback and provides the enable `S_EStopOut`. Without start reset and without auto reset, a reset via `I8` is always required.
- **StartGate**: Lets START through only when enabled
    - **Type**: [`iec61499::events::E_PERMIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_PERMIT/)
    - **Parameters**: none
    - **Explanation**: Passes on the event from the `START` button only if `PERMIT` (from `EmergencyStop.S_EStopOut`) is set.
- **MainSR**: Main set/reset latch
    - **Type**: [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/)
    - **Parameters**: none
    - **Explanation**: Set via `S` and reset via `R`; the state `Q` goes to the output as an adapter.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Puts the state of `MainSR.Q` on the physical output `Q1`.
- **INIT**: Start pulse
    - **Type**: [`iec61131::booleanOperators::INIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/iec61131-3-bool/booleanOperators/INIT/)
    - **Parameters**: none
    - **Explanation**: `INITO` is fed back to `REQ`, `CNF` starts the cyclic block `Cycle`.
- **Cycle**: Cyclic evaluation of `SF_EmergencyStop` and `SF_ResetButton`
    - **Type**: [`iec61499::events::E_CYCLE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_CYCLE/)
    - **Parameters**: `DT = T#20ms`
    - **Explanation**: Generates an event `EO` every 20 ms for `EmergencyStop.REQ` and `ResetButton.REQ`.
- **minTimeOut**: Timer for `TrailingMinimum`
    - **Type**: [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/)
    - **Parameters**: none
    - **Explanation**: Connected to `ResetButton.minTimeOut` via the adapter `TimeOutSocket`.
- **maxTimeOut**: Timer for `TrailingMaximum`
    - **Type**: [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/)
    - **Parameters**: none
    - **Explanation**: Connected to `ResetButton.maxTimeOut` via the adapter `TimeOutSocket`.

## Program Flow and Connections

1. `START.IND` → `StartGate.EI` and `StartGate.EO` → `MainSR.S` (EventConnections): A START click sets the latch only if `StartGate` is enabled.
2. `EmergencyStop.S_EStopOut` → `StartGate.PERMIT` (DataConnection): The enable of the emergency stop block decides about `StartGate`.
3. `STOP.IND` → `MainSR.R` and `EStopFeedback.IND` → `MainSR.R` (EventConnections): Both the manual stop (`I2`) and every change of the emergency stop feedback (`I7`) reset the latch.
4. `EStopFeedback.IN` → `EmergencyStop.S_EStopIn` (DataConnection): The level of the emergency stop circuit is evaluated by `SF_EmergencyStop`.
5. `INIT.INITO` → `INIT.REQ`, `INIT.CNF` → `Cycle.START`, `Cycle.EO` → `EmergencyStop.REQ` and `Cycle.EO` → `ResetButton.REQ` (EventConnections, hidden in the network image): After startup, both safety blocks are triggered cyclically every 20 ms.
6. `ResetInput.IN` → `ResetButton.ResetIn` and `ResetButton.ResetOut` → `EmergencyStop.Reset` (DataConnections): The RESET button `I8` runs via `SF_ResetButton` into the emergency stop block.
7. `ResetButton.EResetOut` → `EmergencyStop.EReset` (EventConnection): The reset event is passed on to `SF_EmergencyStop`.
8. `EmergencyStop.ResetRequest` → `ResetButton.ResetRequested` (DataConnection): The coupling according to Figure 13 – `SF_ResetButton` only monitors when a reset has been requested.
9. `ResetButton.minTimeOut` → `minTimeOut.TimeOutSocket` and `ResetButton.maxTimeOut` → `maxTimeOut.TimeOutSocket` (AdapterConnections): The time monitoring of the reset button.
10. `MainSR.Q` → `DigitalOutput_Q1.OUT` (AdapterConnection): The latch state controls the output `Q1`.

## Summary

Exercise 006_SAFE_AX_02 replaces the hand-built start enable made of `StartPermit` and `StartGate` with the PLCopen standard block `SF_EmergencyStop` together with `SF_ResetButton`. Because neither start reset nor auto reset is active, a deliberate RESET via `I8` is always required after a detected stop before `START` can set the output `Q1` again.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
