# Uebung_028c_AR: Analog Input Calibration with INI Adapters, Hysteresis Controller at the Output and Display

![Uebung_028c_AR_network](./Uebung_028c_AR_network.svg)

* * * * * * * * * *

## Introduction

This exercise calibrates the analog input `I7` with the block `AR_CALIBRATE`; the calibration values `OFFSET` and `SCALE` are stored in an INI file via two `INI_AR2` blocks. The calibrated value `Y` is distributed via `AR_SPLIT_2`: one branch goes to a numeric display (`Q_NumericValue_PHYSA`), the other to a hysteresis block (`Hysteresis_AR_AX`), whose result switches the digital output `Q2`. Threshold and hysteresis are provided by the two sub-apps `THRESHOLD` and `HYSTERESIS` (`INI_IN_AND_STORE_AR`). The button `I1` switches the output `Q1` and, via `AX_SPLIT_2`, is also connected to `SREQ` of the analog input.

> **Important**: Double conversion. A `AD_TO_AR` would act like a "reinterpret_cast".

> **Example** (comment in the network): `0.0` corresponds to 0.5 V, `100.0` corresponds to 4.5 V (typical automotive sensor).

## Function Blocks (FBs) Used

- **DigitalInput_I1**: logiBUS digital input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Provides the state of `I1` as an adapter.
- **AX_SPLIT_2**: Distributes the adapter to two destinations
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: `OUT1` goes to `DigitalOutput_Q1`, `OUT2` to `AnalogInput_I7.SREQ`.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs `AX_SPLIT_2.OUT1` at the physical output `Q1`.
- **AnalogInput_I7**: logiBUS analog input
    - **Type**: [`logiBUS::io::AI::logiBUS_AI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/AI/logiBUS_AI_IDA/)
    - **Parameters**: `QI = TRUE`, `Input = AnalogInput_I7`, `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Explanation**: Provides the analog value of `I7` via the adapter `IN`.
- **AD_TO_AUDI**: first conversion
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AUDI/AD_TO_AUDI/)
    - **Parameters**: none
    - **Explanation**: Converts `AD_IN` to `AUDI_OUT`.
- **AUDI_TO_AR**: second conversion
    - **Type**: [`adapter::conversion::unidirectional::AUDI_TO_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AR_AUDI/AUDI_TO_AR/)
    - **Parameters**: none
    - **Explanation**: Converts `AUDI_IN` to `AR_OUT`.
- **DigitalInput_I2_CO**: Button for the event `CO`
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I2` as `IND`.
- **DigitalInput_I3_CS**: Button for the event `CS`
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I3` as `IND`.
- **CALIBRATE**: Calibration
    - **Type**: [`adapter::Engineering::measurements::AR_CALIBRATE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/Engineering/measurements/AR_CALIBRATE/)
    - **Parameters**: `Y_Offset = 0.0`, `Y_Scale = 100.0`
    - **Explanation**: Receives the measured value via `X` as well as the events `CO` and `CS`; provides the calibrated value `Y` as well as `OFFSET` and `SCALE`.
- **INI_OFFSET**: Storage of `OFFSET` in the INI file
    - **Type**: [`eclipse4diac::storage::INI_AR2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/storage/ini/INI_AR2/)
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AR'`, `KEY = 'OFFSET'`, `DEFAULT_VALUE = 0.0`
    - **Explanation**: Stores the value of `CALIBRATE.OFFSET` (input `VAL`).
- **INI_SCALE**: Storage of `SCALE` in the INI file
    - **Type**: [`eclipse4diac::storage::INI_AR2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/storage/ini/INI_AR2/)
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AR'`, `KEY = 'SCALE'`, `DEFAULT_VALUE = 1.0`
    - **Explanation**: Stores the value of `CALIBRATE.SCALE` (input `VAL`).
- **AR_SPLIT_2**: Distribution of the calibrated value
    - **Type**: [`adapter::events::unidirectional::AR_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/REAL/AR_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: `OUT1` goes to the display, `OUT2` to the hysteresis block.
- **Q_NumericValue_PHYSA**: numeric display
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = InputNumber_PWM_DUTY_OUT_N`
    - **Explanation**: Displays the calibrated value received via `rPhys`.
- **Hysteresis_AR_AX**: hysteresis block
    - **Type**: [`logiBUS::signalprocessing::hysteresis::Hysteresis_AR_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/signalprocessing/hysteresis/Hysteresis_AR_AX/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Receives `INPUT`, `THRESHOLD` and `HYSTERESIS` as `REAL` adapters and provides `OUTPUT` as a digital adapter.
- **DigitalOutput_Q2**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q2`
    - **Explanation**: Outputs `Hysteresis_AR_AX.OUTPUT` at the physical output `Q2`.

### Sub-Blocks: `THRESHOLD` and `HYSTERESIS`

- **THRESHOLD** and **HYSTERESIS**: Instances of [`MyLib::sys::INI_IN_AND_STORE_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/INI_IN_AND_STORE_AR/)
    - **Parameters `THRESHOLD`**: `KEY = 'THRESHOLD'`, `SECTION = 'HYSTERESIS'`, `stObj = InputNumber_THRESHOLD_N`
    - **Parameters `HYSTERESIS`**: `KEY = 'HYSTERESIS'`, `SECTION = 'HYSTERESIS'`, `stObj = InputNumber_HYSTERESIS_N`
    - **Explanation**: Internally a `NumericValue_PHYSA` (input), an `INI_AR` (storage under `SECTION`/`KEY`) and a `Q_NumericValue_PHYSA` (display); the value is output via the plug `VALUEO`.

## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_SPLIT_2.IN`; `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT` and `AX_SPLIT_2.OUT2` → `AnalogInput_I7.SREQ` (AdapterConnections).
2. `AnalogInput_I7.IN` → `AD_TO_AUDI.AD_IN` → `AD_TO_AUDI.AUDI_OUT` → `AUDI_TO_AR.AUDI_IN` → `AUDI_TO_AR.AR_OUT` → `CALIBRATE.X` (AdapterConnections): The analog value is converted twice.
3. `DigitalInput_I2_CO.IND` → `CALIBRATE.CO` and `DigitalInput_I3_CS.IND` → `CALIBRATE.CS` (EventConnections).
4. `CALIBRATE.OFFSET` → `INI_OFFSET.VAL` and `CALIBRATE.SCALE` → `INI_SCALE.VAL` (AdapterConnections): Storage of the calibration values.
5. `CALIBRATE.Y` → `AR_SPLIT_2.IN`; `AR_SPLIT_2.OUT1` → `Q_NumericValue_PHYSA.rPhys` and `AR_SPLIT_2.OUT2` → `Hysteresis_AR_AX.INPUT` (AdapterConnections): The calibrated value goes to the display and to the hysteresis block.
6. `THRESHOLD.VALUEO` → `Hysteresis_AR_AX.THRESHOLD` and `HYSTERESIS.VALUEO` → `Hysteresis_AR_AX.HYSTERESIS` (AdapterConnections): Threshold and hysteresis.
7. `Hysteresis_AR_AX.OUTPUT` → `DigitalOutput_Q2.OUT` (AdapterConnection): The hysteresis result switches `Q2`.

## Summary

Exercise 028c_AR extends the calibration from `Uebung_028a_AX` with a display of the calibrated value and a hysteresis controller, whose threshold and hysteresis are entered via the sub-apps `THRESHOLD` and `HYSTERESIS` and stored in the INI section `HYSTERESIS`. All signals run consistently via adapters; the double conversion `AD_TO_AUDI` and `AUDI_TO_AR` is important here, because a single `AD_TO_AR` would merely reinterpret the value.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
- [🌐 The PWM Signal & Infographic on ms-muc-docs.de](https://www.ms-muc-docs.de/automatisierung/das-pwm-signal-die-kunst-spannung-zu-zerhacken/das-pwm-signal-die-kunst-spannung-zu-zerhacken-website/)
