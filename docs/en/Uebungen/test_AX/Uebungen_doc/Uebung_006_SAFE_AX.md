# Exercise_006_SAFE_AX: SAFE Variant with Restart Lock (SF_ResetButton)

![Uebung_006_SAFE_AX_network](./Uebung_006_SAFE_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the SAFE variant of `Uebung_006_AX` (SR flip-flop with START/STOP buttons): STOP (`I2`) and the feedback input `I8` (feedback of the hardware emergency stop) switch the output off immediately in software and lock START via an enable latch (`E_RS`) and a start gate (`E_PERMIT`). The lock is only released when RESET (`I7`) has detected a valid button edge via `SF_ResetButton` (PLCopen Safety Part 1, Ch. 6.1). RESET alone does not switch the output back on; START has to be pressed again for that.

**Cold start:** `StartPermit.Q` is `FALSE` (type default of `BOOL`, `E_RS` has no initial value). START therefore remains locked until a valid RESET acknowledgement has been given after startup.

## Function Blocks (FBs) Used

- **START**: START button
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I1` as event `IND`.
- **STOP**: Manual stop (not the hardware emergency stop)
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I2` as event `IND`.
- **StopFeedback**: Feedback input of the hardware emergency stop
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I8`, `InputEvent = BUTTON_PRESS_DOWN`
    - **Explanation**: Reports pressing down (`BUTTON_PRESS_DOWN`) on `I8` as event `IND`.
- **ResetInput**: Level and event for `SF_ResetButton`
    - **Type**: [`logiBUS::io::DI::logiBUS_IX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IX/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I7`
    - **Explanation**: Provides the level of `I7` (`IN`) and the associated event `IND`.
- **ResetButton**: Reset button according to PLCopen Safety
    - **Type**: `logiBUS::safety::PLCopen::SF_ResetButton`
    - **Parameters**: `ResetRequested = TRUE`, `TrailingMinimum = T#350ms`, `TrailingMaximum = T#2s`
    - **Explanation**: Adds a trailing-edge evaluation (LOW-HIGH-LOW) to the reset input: after a valid press and release of `I7` (inputs `REQ` and `ResetIn`) within `TrailingMinimum` to `TrailingMaximum`, the event `EResetOut` is output. The time limits are monitored by two `E_TimeOut` blocks via the adapters `minTimeOut`/`maxTimeOut`.
- **minTimeOut**: Timer for the minimum time of `SF_ResetButton`
    - **Type**: [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/)
    - **Parameters**: none
    - **Explanation**: Connected to `ResetButton.minTimeOut` via `TimeOutSocket`.
- **maxTimeOut**: Timer for the maximum time of `SF_ResetButton`
    - **Type**: [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/)
    - **Parameters**: none
    - **Explanation**: Connected to `ResetButton.maxTimeOut` via `TimeOutSocket`.
- **StartPermit**: Enable latch (`E_RS` instead of `E_SR` only for crossing-free wiring)
    - **Type**: [`iec61499::events::E_RS`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_RS/)
    - **Parameters**: none
    - **Explanation**: `S` (from `ResetButton.EResetOut`) enables START, `R` (from `StopFeedback.IND`) locks START. The output `Q` feeds `StartGate.PERMIT`.
- **StartGate**: Only lets START pass when enabled
    - **Type**: [`iec61499::events::E_PERMIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_PERMIT/)
    - **Parameters**: none
    - **Explanation**: Forwards the START event (`EI`) as `EO` only if `PERMIT` (from `StartPermit.Q`) is `TRUE`.
- **MainSR**: Main memory element for the output
    - **Type**: [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/)
    - **Parameters**: none
    - **Explanation**: `S` sets the output (only via `StartGate`), `R` resets it (by STOP or `StopFeedback`). The adapter output `Q` leads to the digital output.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Passes the state of `MainSR.Q` to the physical output `Q1`.

## Program Flow and Connections

1. `START.IND` → `StartGate.EI` and `StartGate.EO` → `MainSR.S` (EventConnections): A click on `I1` sets the output only if `StartGate` is enabled.
2. `STOP.IND` → `MainSR.R` (EventConnection): A click on `I2` resets the output immediately.
3. `StopFeedback.IND` → `MainSR.R` and `StopFeedback.IND` → `StartPermit.R` (EventConnections): The hardware emergency stop feedback on `I8` switches the output off and additionally locks START by resetting the enable latch.
4. `ResetInput.IND` → `ResetButton.REQ` (EventConnection) and `ResetInput.IN` → `ResetButton.ResetIn` (DataConnection): Level and event of `I7` reach `SF_ResetButton`.
5. `ResetButton.minTimeOut` → `minTimeOut.TimeOutSocket` and `ResetButton.maxTimeOut` → `maxTimeOut.TimeOutSocket` (AdapterConnections): The two timers monitor the valid time window of the button edge.
6. `ResetButton.EResetOut` → `StartPermit.S` (EventConnection) and `StartPermit.Q` → `StartGate.PERMIT` (DataConnection): A valid RESET acknowledgement enables START again.
7. `MainSR.Q` → `DigitalOutput_Q1.OUT` (AdapterConnection): The state of the memory element is applied to the physical output `Q1`.

**Behavior**: START (`I1`) only switches `Q1` on if an enable is present. STOP (`I2`) or the emergency stop feedback (`I8`) switch `Q1` off; the feedback additionally locks START. After the lock, `Q1` initially stays off even after a valid RESET acknowledgement (`I7`); only a new START click switches `Q1` on again.

## Summary

This exercise extends the simple SR flip-flop from `Uebung_006_AX` with a restart lock: the enable latch `StartPermit` and the gate `StartGate` block START after an emergency stop feedback until `SF_ResetButton` has detected a valid RESET edge. At cold start, START is also locked because `StartPermit.Q` has the type default `FALSE`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
