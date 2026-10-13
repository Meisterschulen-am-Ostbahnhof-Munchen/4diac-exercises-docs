# Uebung_028a_AX: Analog Input Calibration with INI Adapters

![Uebung_028a_AX_network](./Uebung_028a_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise calibrates an analog input (`I4`) with the block `AR_CALIBRATE`. The analog value is carried as an adapter and converted into a `REAL` value via a double conversion (`AD_TO_AUDI`, then `AUDI_TO_AR`). The calculated calibration values `OFFSET` and `SCALE` are stored in an INI file via two `INI_AR2` blocks.

> **Important**: Double conversion. A single `AD_TO_AR` would act like a "reinterpret_cast".

## Function Blocks (FBs) Used

- **DigitalInput_I1**: logiBUS digital input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Provides the state of `I1` as an adapter.
- **AX_SPLIT_2**: Distributes the adapter to two destinations
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: `OUT1` goes to the output `Q1`, `OUT2` to `AnalogInput_I4.SREQ`.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs the value of `AX_SPLIT_2.OUT1` at the physical output `Q1`.
- **AnalogInput_I4**: logiBUS analog input
    - **Type**: [`logiBUS::io::AI::logiBUS_AI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/AI/logiBUS_AI_IDA/)
    - **Parameters**: `QI = TRUE`, `Input = AnalogInput_I4`, `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Explanation**: Provides the analog value of `I4` via the adapter `IN`.
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
    - **Parameters**: `Y_Offset = 100.0`, `Y_Scale = 600.0`
    - **Explanation**: Receives the converted measured value via `X` and the events `CO` and `CS`; provides `OFFSET` and `SCALE` as adapters.
- **INI_OFFSET**: Storage of `OFFSET` in the INI file
    - **Type**: [`eclipse4diac::storage::INI_AR2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/storage/ini/INI_AR2/)
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AX'`, `KEY = 'OFFSET'`, `DEFAULT_VALUE = 0.0`
    - **Explanation**: Stores the value of `CALIBRATE.OFFSET` (input `VAL`) under the key `OFFSET`.
- **INI_SCALE**: Storage of `SCALE` in the INI file
    - **Type**: [`eclipse4diac::storage::INI_AR2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/storage/ini/INI_AR2/)
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AX'`, `KEY = 'SCALE'`, `DEFAULT_VALUE = 1.0`
    - **Explanation**: Stores the value of `CALIBRATE.SCALE` (input `VAL`) under the key `SCALE`.

## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_SPLIT_2.IN`; `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT` and `AX_SPLIT_2.OUT2` → `AnalogInput_I4.SREQ` (AdapterConnections): `I1` switches `Q1` and is at the same time connected to `SREQ` of the analog input.
2. `AnalogInput_I4.IN` → `AD_TO_AUDI.AD_IN` → `AD_TO_AUDI.AUDI_OUT` → `AUDI_TO_AR.AUDI_IN` → `AUDI_TO_AR.AR_OUT` → `CALIBRATE.X` (AdapterConnections): The analog value is converted twice and passed to `CALIBRATE` as measured value `X`.
3. `DigitalInput_I2_CO.IND` → `CALIBRATE.CO` and `DigitalInput_I3_CS.IND` → `CALIBRATE.CS` (EventConnections): The two buttons `I2` and `I3` trigger the events `CO` and `CS`.
4. `CALIBRATE.OFFSET` → `INI_OFFSET.VAL` and `CALIBRATE.SCALE` → `INI_SCALE.VAL` (AdapterConnections): The calibration values are stored in the INI file.

## Summary

Exercise 028a_AX shows the calibration of an analog input with `AR_CALIBRATE`, in which the measured value and the calibration values are carried exclusively via adapters. The double conversion `AD_TO_AUDI` and `AUDI_TO_AR` is important; a single `AD_TO_AR` would merely reinterpret the value ("reinterpret_cast"). `OFFSET` and `SCALE` are stored via `INI_AR2` in the INI section `Uebung_028a_AX`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
