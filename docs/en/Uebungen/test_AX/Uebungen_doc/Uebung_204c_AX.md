# Exercise_204c_AX: Interlock ILOCK_CONFLICT_TRIP_PROTECT_AX (Trip on conflict AND protection time after release, via adapter)

![Uebung_204c_AX_network](./Uebung_204c_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise extends `Uebung_204_AX` (which uses the simpler `ILOCK_CONFLICT_TRIP_AX` without a protection time) by adding an extra protection time: After the active input is released, the interlock waits `DT_PROTECT` (here 1 second) before taking over a new direction. This wait applies only to taking over a new direction — a conflict that occurs while the first input is still actively held (i.e. before it is released) still triggers a TRIP immediately, regardless of `DT_PROTECT` (unchanged from `ILOCK_CONFLICT_TRIP`). If, once `DT_PROTECT` has elapsed, the other input is still (or again) active, the resulting re-evaluation triggers a TRIP instead of the new direction. This is the same protection time logic as in `ILOCK_SWITCH_PROTECT_AX` (`Uebung_205_AX`), here combined with the conflict detection (TRIP) from `Uebung_204_AX`.

## Function Blocks (FBs) Used

- **DigitalInput_I1**, **DigitalInput_I2**: logiBUS digital inputs (Type: `logiBUS::io::DI::logiBUS_IXA`)

- **Parameters**: QI = TRUE, Input = Input_I1 or Input_I2

- **Explanation**: These deliver the two mutually exclusive request signals (e.g., Up/Down) as AX adapter signals to the interlock.

- **DigitalInput_Reset**: logiBUS event input (Type: `logiBUS::io::DI::logiBUS_IE`)

- **Parameters**: QI = TRUE, Input = Input_I3, InputEvent = BUTTON_SINGLE_CLICK

- **Explanation**: Triggers the reset (`EI_RESET`) of the interlock upon a button click after a conflict (TRIP) has occurred.

- **ILOCK_AX**: Interlock function block (Type: `logiBUS::signalprocessing::interlock::ILOCK_CONFLICT_TRIP_PROTECT_AX`)

- **Parameters**: DT_PROTECT = T#1s

- **Explanation**: Interlocks the two inputs `UP_IN`/`DOWN_IN` mutually. If both are active simultaneously while one direction is still actively held, the function block immediately detects a conflict (TRIP) and sets `TRIP_OUT` — regardless of `DT_PROTECT`. After releasing the previously active input, it waits for `DT_PROTECT` and then re-evaluates the current input state: if only one direction is active, it is taken over; if both are (again) active, a TRIP is triggered instead.

- **DigitalOutput_Q1**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: QI = TRUE, Output = Output_Q1

- **Explanation**: Passes the released `UP_OUT` signal to the peripheral device.

- **DigitalOutput_Q2**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: QI = TRUE, Output = Output_Q2

- **Description**: Passes the enabled `DOWN_OUT` signal to the peripheral device.

- **Trip_Display**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: QI = TRUE, Output = Output_Q4

- **Description**: Indicates the conflict state (`TRIP_OUT`), e.g., via an indicator light.

- **E_TimeOut**: Timer event source (Type: `iec61499::events::E_TimeOut`)

- **Parameters**: None

- **Explanation**: Provides the periodic time event to the Interlock function block via the adapter connection `timeOut`, which is then used to internally evaluate `DT_PROTECT`.

### Sub-function blocks: None

This exercise does not use any further sub-function blocks; all function blocks are located directly at the top level of the SubApp.

## Program flow and connections

1. `DigitalInput_I1.IN` → `ILOCK_AX.UP_IN` and `DigitalInput_I2.IN` → `ILOCK_AX.DOWN_IN`: The two request signals are passed to the interlock function block via adapter connections.
2. `DigitalInput_Reset.IND` → `ILOCK_AX.EI_RESET`: A click on the reset button clears a triggered conflict.
3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`: The interlock block uses this to obtain the time base for the protection time monitoring `DT_PROTECT`.
4. **Normal Operation**: If only one input is active, `UP_OUT` or `DOWN_OUT` is enabled and output via `Output_Q1`/`Output_Q2`.

5. **Conflict Case (TRIP)**: If `UP_IN` and `DOWN_IN` are active simultaneously, `ILOCK_AX.TRIP_OUT` sets → `Trip_Anzeige.OUT` and blocks both outputs until reset via `DigitalInput_Reset`.

6. **Protection Time**: If the active input is released, `ILOCK_AX` waits `DT_PROTECT` (1 s) and then re-evaluates the current input state: if only one direction is active, it is taken over; if both inputs are active (because the other one was activated in the meantime and is still pending), a TRIP is triggered instead. A conflict that occurs while the first input is still held (i.e. before it is released), by contrast, still triggers a TRIP immediately, without waiting for `DT_PROTECT`.

## Summary

Exercise 204c_AX combines the conflict detection from `Uebung_204_AX` with a protection time after the active input is enabled, as introduced in `Uebung_205_AX` for simple direction changes. `ILOCK_CONFLICT_TRIP_PROTECT_AX` thus prevents not only simultaneous, conflicting requests, but also excessively rapid direction changes immediately after enabling – a typical requirement for robust interlock logic in automation technology.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
