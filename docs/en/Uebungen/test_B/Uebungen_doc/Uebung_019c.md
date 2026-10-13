# Exercise_019c: Switching a Screen

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/a6872e59-1dfc-4132-a118-aff1bc7bc944)

![Uebung_019c_network](./Uebung_019c_network.svg)

* * * * * * * * * *

## Introduction

This article describes the logiBUS® exercise `Uebung_019c`. This is the most complex navigation logic, where screen switching can be actively blocked by the hardware state: as long as the alarm input `I3` is active, the navigation events are not passed on to the mask selection.

The goal is to implement conditional navigation control. Switching between screen pages should be prevented as long as an active alarm is present.

## Function Blocks (FBs) Used

- **DigitalInput_CLK_I1**: logiBUS input with click detection
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I1` as event `IND` (request: mask 1).
- **DigitalInput_CLK_I2**: logiBUS input with click detection
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I2` as event `IND` (request: mask 2).
- **ACK**: physical acknowledge button instead of a softkey
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I4` as event `IND`.
- **Alarmeingang**: physical alarm sensor
    - **Type**: `logiBUS::io::DI::logiBUS_IX`
    - **Parameters**: `QI = TRUE`, `Input = Input_I3`
    - **Explanation**: Provides the state `IN` of `I3` together with the event `IND`. As long as `IN` is TRUE, an alarm state is in effect.
- **E_PERMIT_INVERT_3**: three-channel inverted event permit gate (SubApp instance)
    - **Type**: `MyLib::sys::E_PERMIT_INVERT_3`
    - **Parameters**: none
    - **Explanation**: Passes the events `EI1`..`EI3` on as `EO1`..`EO3` only if `PERMIT` is FALSE. Internally the block consists of `F_NOT_BOOL_INIT` (inverts `PERMIT`) and `E_PERMIT_3`. Here `PERMIT` is fed by `Alarmeingang`: no alarm = events pass, alarm = events are blocked.
- **F_SEL_E_4**: event-driven selection from four values
    - **Type**: `eclipse4diac::utils::selection::F_SEL_E_4`
    - **Parameters**: `IN1 = DataMask_M1`, `IN2 = DataMask_M2`, `IN3 = DataMask_M1`, `IN4 = AlarmMask_A2_medium`
    - **Explanation**: Depending on which event `REQ1`..`REQ4` arrives, the corresponding value `IN1`..`IN4` is placed on `OUT` and `CNF` is reported. `REQ1`, `REQ2` and `REQ3` are connected; `REQ4` (and thus `AlarmMask_A2_medium`) is not wired in this network.
- **Q_ActiveMask**: change of the active mask
    - **Type**: `isobus::UT::Q::Q_ActiveMask`
    - **Parameters**: none
    - **Explanation**: Sets the mask ID supplied by `F_SEL_E_4` (`u16NewMaskId`) as the active mask, triggered by `REQ`.
- **E_R_TRIG_Alarm**: permit gate for the alarm start
    - **Type**: `iec61499::events::E_PERMIT`
    - **Parameters**: none
    - **Explanation**: Forwards the event `IND` of `Alarmeingang` as `EO` only if `PERMIT` (= `Alarmeingang.IN`) is TRUE.
- **E_SR**: alarm memory
    - **Type**: `iec61499::events::E_SR`
    - **Parameters**: none
    - **Explanation**: `S` sets the memory (alarm occurred), `R` resets it (acknowledged). Output `Q` with event `EO`.
- **Alarmausgang**: logiBUS digital output for the alarm indication
    - **Type**: `logiBUS::io::DQ::logiBUS_QX`
    - **Parameters**: `QI = TRUE`, `Output = Output_Q8`
    - **Explanation**: Switches the physical output `Q8` according to `E_SR.Q`.

## Program Flow and Connections

1. `DigitalInput_CLK_I1.IND` → `E_PERMIT_INVERT_3.EI1`, `DigitalInput_CLK_I2.IND` → `E_PERMIT_INVERT_3.EI2` and `ACK.IND` → `E_PERMIT_INVERT_3.EI3`: all three operating events first pass through the inverted permit gate.
2. `Alarmeingang.IN` → `E_PERMIT_INVERT_3.PERMIT`: the gate checks the alarm input before every action.
    - If **no** alarm is present (`IN = FALSE`), the event is passed through: `E_PERMIT_INVERT_3.EO1` → `F_SEL_E_4.REQ1`, `EO2` → `REQ2`, `EO3` → `REQ3`. The page changes.
    - If an alarm is active (`IN = TRUE`), the event is blocked. The page change is **ignored**.
3. `F_SEL_E_4.OUT` → `Q_ActiveMask.u16NewMaskId` and `F_SEL_E_4.CNF` → `Q_ActiveMask.REQ`: the selected mask (`DataMask_M1` for `I1`, `DataMask_M2` for `I2`, `DataMask_M1` for `ACK`) becomes active.
4. `Alarmeingang.IND` → `E_R_TRIG_Alarm.EI` and `Alarmeingang.IN` → `E_R_TRIG_Alarm.PERMIT`: an event from `I3` is forwarded only while the alarm is active (`IN = TRUE`).
5. `E_R_TRIG_Alarm.EO` → `E_SR.S`: the alarm memory is set.
6. `E_PERMIT_INVERT_3.EO3` → `E_SR.R`: the acknowledge button `I4` resets the memory, but only if the alarm input `I3` is FALSE again (otherwise the gate blocks the event).
7. `E_SR.EO` → `Alarmausgang.REQ` and `E_SR.Q` → `Alarmausgang.OUT`: the output `Q8` follows the alarm memory.

**Behavior**: As long as `I3` is active, `I1`, `I2` and `I4` have no effect. Only when the alarm sensor (`I3`) is FALSE again **AND** the user presses the acknowledge button (`I4`) is the memory reset and, at the same time, `DataMask_M1` selected; navigation is then free again.

## Summary

The exercise shows how an inverted event permit gate (`E_PERMIT_INVERT_3`) makes the mask switching dependent on the hardware state: an active alarm locks the navigation, and only acknowledging after the alarm has been cleared releases it again.

**Application example - mandatory troubleshooting**: In the event of a critical hardware fault (e.g., emergency stop activated), the operator must not continue using the terminal for normal machine control. They will be "held" on the alarm screen until the emergency stop is released and the fault acknowledged. This forces attention to be paid to the primary safety issue.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
