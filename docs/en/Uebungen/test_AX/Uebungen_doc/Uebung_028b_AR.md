# Exercise_028b_AR: Analog Input Calibration with INI Adapters and Hysteresis Controller at the Output

![Uebung_028b_AR_network](./Uebung_028b_AR_network.svg)

* * * * * * * * * *

## Introduction

This exercise demonstrates the calibration of an analog input signal using adapters and INI-based storage of the calibration parameters. The calibrated signal is then passed through a hysteresis controller whose threshold and hysteresis are entered via the VT and are also stored in the INI file. The exercise shows the combination of analog and digital inputs/outputs, adapter conversions and the persistent storage of parameters.

## Function Blocks (FBs) Used

- **DigitalInput_I2**: logiBUS digital input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`
    - **Explanation**: Provides the state of `I2` as an AX adapter signal.
- **AX_SPLIT_2**: signal splitter
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: Distributes the AX signal of `I2` to two outputs: `OUT1` to the analog input (measurement request), `OUT2` to the digital output `Q1`.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs the signal of `I2` at the physical output `Q1`.
- **AnalogInput_I1**: logiBUS analog input
    - **Type**: [`logiBUS::io::AI::logiBUS_AI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/AI/logiBUS_AI_IDA/)
    - **Parameters**: `QI = TRUE`, `Input = AnalogInput_I1`, `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Explanation**: Provides the analog measured value at the AD adapter `IN`. A measurement can be requested externally via the socket `SREQ`.
- **AD_TO_AR_NUM**: conversion adapter
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AR_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AD_TO_AR_NUM/)
    - **Parameters**: none
    - **Explanation**: Converts the raw value from the AD adapter numerically (AD → UDINT → REAL) into a REAL value at the AR adapter. **Note** (comment in the network): an `AD_TO_AR` would act like a "reinterpret_cast" (bit pattern); `AD_TO_AR_NUM`, in contrast, converts the numeric value.
- **DigitalInput_I3_CO**: click input for the offset calibration
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I3` as event `IND`.
- **DigitalInput_I4_CS**: click input for the scale calibration
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I4` as event `IND`.
- **CALIBRATE**: calibration
    - **Type**: [`adapter::Engineering::measurements::AR_CALIBRATE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/Engineering/measurements/AR_CALIBRATE/)
    - **Parameters**: `Y_Offset = 100.0`, `Y_Scale = 600.0`
    - **Explanation**: Calculates `Y = (X + OFFSET) * SCALE`. The event `CO` determines the offset as `Y_Offset - X`, the event `CS` the scale factor as `Y_Scale / (X + OFFSET)`. Outputs are the calibrated value `Y` and the adapters `OFFSET` and `SCALE`.
- **INI_OFFSET**: INI storage for the offset
    - **Type**: [`eclipse4diac::storage::INI_AR2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/storage/ini/INI_AR2/)
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AR'`, `KEY = 'OFFSET'`, `DEFAULT_VALUE = 0.0`
    - **Explanation**: Reads the value from the INI file and, via the bidirectional adapter `VAL`, writes back the offset calculated by `CALIBRATE`.
- **INI_SCALE**: INI storage for the scale
    - **Type**: [`eclipse4diac::storage::INI_AR2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/storage/ini/INI_AR2/)
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AR'`, `KEY = 'SCALE'`, `DEFAULT_VALUE = 1.0`
    - **Explanation**: Like `INI_OFFSET`, but for the scale factor.
- **THRESHOLD**: VT input and storage of the threshold
    - **Type**: [`MyLib::sys::INI_IN_AND_STORE_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/INI_IN_AND_STORE_AR/) (SubApp instance)
    - **Parameters**: `KEY = 'THRESHOLD'`, `SECTION = 'HYSTERESIS'`, `stObj = InputNumber_THRESHOLD_N`
    - **Explanation**: A physically scaled REAL value entered via the VT (input field `InputNumber_THRESHOLD_N`) is stored in the INI file, shown again on the VT and output at the adapter `VALUEO`.
- **HYSTERESIS**: VT input and storage of the hysteresis width
    - **Type**: [`MyLib::sys::INI_IN_AND_STORE_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/INI_IN_AND_STORE_AR/) (SubApp instance)
    - **Parameters**: `KEY = 'HYSTERESIS'`, `SECTION = 'HYSTERESIS'`, `stObj = InputNumber_HYSTERESIS_N`
    - **Explanation**: Like `THRESHOLD`, but for the hysteresis width (input field `InputNumber_HYSTERESIS_N`).
- **Hysteresis_AR_AX**: hysteresis controller
    - **Type**: [`logiBUS::signalprocessing::hysteresis::Hysteresis_AR_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/signalprocessing/hysteresis/Hysteresis_AR_AX/)
    - **Parameters**: `QI = TRUE`
    - **Explanation**: Switches `OUTPUT` on when `INPUT` reaches or exceeds `THRESHOLD + HYSTERESIS/2`, and off when `INPUT` falls below `THRESHOLD - HYSTERESIS/2`.
- **DigitalOutput_Q2**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q2`
    - **Explanation**: Outputs the result of the hysteresis controller at the physical output `Q2`.

## Program Flow and Connections

1. `DigitalInput_I2.IN` → `AX_SPLIT_2.IN` (AdapterConnection): The state of `I2` is distributed.
2. `AX_SPLIT_2.OUT2` → `DigitalOutput_Q1.OUT`: `Q1` follows `I2`.
3. `AX_SPLIT_2.OUT1` → `AnalogInput_I1.SREQ`: `I2` requests a measurement at the analog input.
4. `AnalogInput_I1.IN` → `AD_TO_AR_NUM.AD_IN`: The measured value is converted numerically to a REAL value.
5. `AD_TO_AR_NUM.AR_OUT` → `CALIBRATE.X`: The converted value is the calibration input.
6. `DigitalInput_I3_CO.IND` → `CALIBRATE.CO` and `DigitalInput_I4_CS.IND` → `CALIBRATE.CS` (EventConnections): A click on `I3` triggers the offset calibration, a click on `I4` the scale calibration.
7. `CALIBRATE.OFFSET` → `INI_OFFSET.VAL` and `CALIBRATE.SCALE` → `INI_SCALE.VAL`: The calculated calibration values are stored in the INI file (section `Uebung_028a_AR`).
8. `CALIBRATE.Y` → `Hysteresis_AR_AX.INPUT`: The calibrated measured value is evaluated.
9. `THRESHOLD.VALUEO` → `Hysteresis_AR_AX.THRESHOLD` and `HYSTERESIS.VALUEO` → `Hysteresis_AR_AX.HYSTERESIS`: Threshold and hysteresis width from the VT inputs.
10. `Hysteresis_AR_AX.OUTPUT` → `DigitalOutput_Q2.OUT`: The hysteresis result switches `Q2`.

**Learning objectives**:

- Understanding adapter conversion between analog signal types (`AD` → `AR`, numerically with `AD_TO_AR_NUM`)
- Working with **INI** blocks for persistent storage and loading of calibration parameters
- Using a calibration adapter (offset/scale)
- Implementing a hysteresis function for threshold monitoring

## Summary

The exercise **Uebung_028b_AR** implements a complete chain for processing an analog input signal: measurement, calibration, storage of the calibration data and subsequent hysteresis evaluation. By combining digital events, adapter conversions and INI-based parameter management, it presents a practical example of industrial analog value processing in 4diac.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
