# Exercise_234_AX: Two-Point Controller with Hysteresis (Hardware-Only Exercise, No Temperature Display)

![Uebung_234_AX_network](./Uebung_234_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements a classic thermostat/level switch pattern: An analog measurement (e.g., level or temperature) is checked against an average value `MI` with a dead zone (`DEAD`) and hysteresis (`HYSTERESIS`) and controls two opposing actuators (e.g., filling/emptying or heating/cooling) – never both simultaneously. The exercise uses only physical hardware, without a temperature display.


## Function Blocks Used (FBs)

- **AnalogInput_I4**: Analog input (Type: `logiBUS::io::AI::logiBUS_AI_IDA`)

- **Parameters**: QI = TRUE, Input = AnalogInput_I4, AnalogInput_hysteresis = 5, TimeDelta = 250, TimeRateLimit = 100

- **Explanation**: Reads the raw analog measurement value (DWORD, type AD) and filters out small signal fluctuations using the input hysteresis of 5.

- **AD_TO_AUDI**: Converts AD (DWORD adapter) to AUDI (UDINT adapter)

- **Parameters**: None

- **Explanation**: First step in the numerically correct conversion of the raw measurement value into a numerical value.

- **AUDI_TO_AR**: Conversion from AUDI (UDINT adapter) to AR (REAL adapter)

- **Parameters**: None

- **Explanation**: Second step of the conversion. Together with `AD_TO_AUDI`, this creates the same two-stage, numerically correct conversion chain that `Uebung_028a_AR` also builds manually ("an AD_TO_AR would be like a reinterpret_cast").

- **HysteresisParams_AR**: Parameter block for the hysteresis thresholds

- **Parameters**: rMI = 500.0, rDEAD = 20.0, rHYSTERESIS = 30.0

- **Explanation**: Provides the three thresholds of the two-point controller as fixed example values. In real-world systems, these would typically be configurable via INI/OPC UA.

- **DualHysteresis**: Two-point controller with dead zone and hysteresis (Type: `logiBUS::signalprocessing::hysteresis::DualHysteresis_AR_A2X`)

- **Parameter**: QI = TRUE

- **Explanation**: Compares the measured value (`INPUT`) against `MI`: If the value rises above `MI + DEAD + HYSTERESIS` (here 550), `UP` switches on; if it falls below `MI - DEAD - HYSTERESIS` (here 450), `DOWN` switches on. Switching off only occurs when the value returns to the pure dead zone `MI ± DEAD` (480–520) – the difference between the switch-on and switch-off points prevents chatter near the switching threshold.

### Sub-modules: A2X_TO_QXA2

- **A2X_TO_QXA2** (Type: `MyLib::sys::A2X_TO_QXA2`)

- **Parameters**: Output_UP = Output_Q1, Output_DOWN = Output_Q2

- **Explanation**: Unbundles the single output signal `A2X` (`UP`/`DOWN`) from `DualHysteresis` to two physical logiBUS outputs (Q1 for UP, Q2 for DOWN).

## Program Flow and Connections

1. `AnalogInput_I4` reads the raw analog value (adapter type `AD`).

2. `AD_TO_AUDI.AD_IN` ← `AnalogInput_I4.IN`: The raw value is first converted into a `AUDI` value (unsigned numerical value).

3. `AUDI_TO_AR.AUDI_IN` ← `AD_TO_AUDI.AUDI_OUT`: The conversion then takes place into a `AR` value (REAL adapter) – the numerically correct, two-stage conversion.

4. `DualHysteresis.INPUT` ← `AUDI_TO_AR.AR_OUT`: The processed measured value is sent to the two-point controller.

5. `DualHysteresis.MI/DEAD/HYSTERESIS` ← `HysteresisParams_AR.MI/DEAD/HYSTERESIS`: The threshold values (500.0 / 20.0 / 30.0) are fixed.

6. `DualHysteresis.OUT` → `A2X_TO_QXA2.IN`: The combined UP/DOWN result is passed to the output block.

7. `A2X_TO_QXA2` writes UP to `Output_Q1` and DOWN to `Output_Q2` – the two actuators are never activated simultaneously.


## Summary

Exercise 234_AX demonstrates the classic hardware-based design of a two-position controller with hysteresis: An analog input is processed via a two-stage, numerically correct adapter chain (`AD_TO_AUDI` → `AUDI_TO_AR`), checked against a mean value with dead zone and hysteresis by `DualHysteresis_AR_A2X`, and then routed to two mutually exclusive digital outputs via `A2X_TO_QXA2`. For the same function with VT display (measured value as a numeric field, UP/DOWN as background color instead of physical outputs), see `Uebung_235_AX`.


---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
