# Uebung_028a_AX: Analog Input Calibration with INI Adapters

![Uebung_028a_AX_network](./Uebung_028a_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise calibrates the analog input `I1` with the block `AR_CALIBRATE`. The analog value is carried as an adapter and converted numerically (AD → UDINT → REAL) into a `REAL` value by the block `AD_TO_AR_NUM`. The calculated calibration values `OFFSET` and `SCALE` are stored in an INI file via two `INI_AR2` blocks.

> **Important**: An `AD_TO_AR` would act like a "reinterpret_cast" (bit pattern). `AD_TO_AR_NUM`, in contrast, converts the numeric value (AD → UDINT → REAL).

## Function Blocks (FBs) Used

- **DigitalInput_I2**: logiBUS digital input
    - **Type**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`
    - **Explanation**: Provides the state of `I2` as an adapter.
- **AX_SPLIT_2**: Distributes the adapter to two destinations
    - **Type**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameters**: none
    - **Explanation**: `OUT1` goes to `AnalogInput_I1.SREQ`, `OUT2` to the output `Q1`.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Outputs the value of `AX_SPLIT_2.OUT2` at the physical output `Q1`.
- **AnalogInput_I1**: logiBUS analog input
    - **Type**: [`logiBUS::io::AI::logiBUS_AI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/AI/logiBUS_AI_IDA/)
    - **Parameters**: `QI = TRUE`, `Input = AnalogInput_I1`, `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Explanation**: Provides the analog value of `I1` via the adapter `IN`.
- **AD_TO_AR_NUM**: numeric conversion
    - **Type**: [`adapter::conversion::unidirectional::AD_TO_AR_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AD_TO_AR_NUM/)
    - **Parameters**: none
    - **Explanation**: Converts `AD_IN` numerically (AD → UDINT → REAL) to `AR_OUT`.
- **DigitalInput_I3_CO**: Button for the event `CO`
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I3` as `IND`.
- **DigitalInput_I4_CS**: Button for the event `CS`
    - **Type**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameters**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: Reports a single click on `I4` as `IND`.
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

1. `DigitalInput_I2.IN` → `AX_SPLIT_2.IN`; `AX_SPLIT_2.OUT1` → `AnalogInput_I1.SREQ` and `AX_SPLIT_2.OUT2` → `DigitalOutput_Q1.OUT` (AdapterConnections): `I2` switches `Q1` and is at the same time connected to `SREQ` of the analog input.
2. `AnalogInput_I1.IN` → `AD_TO_AR_NUM.AD_IN` and `AD_TO_AR_NUM.AR_OUT` → `CALIBRATE.X` (AdapterConnections): The analog value is converted numerically and passed to `CALIBRATE` as measured value `X`.
3. `DigitalInput_I3_CO.IND` → `CALIBRATE.CO` and `DigitalInput_I4_CS.IND` → `CALIBRATE.CS` (EventConnections): The two buttons `I3` and `I4` trigger the events `CO` and `CS`.
4. `CALIBRATE.OFFSET` → `INI_OFFSET.VAL` and `CALIBRATE.SCALE` → `INI_SCALE.VAL` (AdapterConnections): The calibration values are stored in the INI file.

## Summary

Exercise 028a_AX shows the calibration of an analog input with `AR_CALIBRATE`, in which the measured value and the calibration values are carried exclusively via adapters. The numeric conversion with `AD_TO_AR_NUM` (AD → UDINT → REAL) is important; a plain `AD_TO_AR` would merely reinterpret the value ("reinterpret_cast"). `OFFSET` and `SCALE` are stored via `INI_AR2` in the INI section `Uebung_028a_AX`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
