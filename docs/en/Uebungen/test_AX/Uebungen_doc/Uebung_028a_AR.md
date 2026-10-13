# Uebung_028a_AR: Analog Input Calibration with INI Adapters

![Uebung_028a_AR_network](./Uebung_028a_AR_network.svg)

* * * * * * * * * *

## Introduction

This exercise demonstrates the calibration of an analog input signal using adapters and the storage of the calibration parameters (offset and scale) in an INI file. The function block `AR_CALIBRATE` performs the two-point calibration. The calibration is triggered via two digital inputs (`Input_I2`, `Input_I3`), and the results are stored in two separate storage blocks (`INI_AR2`). The conversion between the adapter types `AD` and `AR` is done by two adapter converters connected in series.

## Function Blocks (FBs) Used

The exercise uses only directly instantiated function blocks (no sub-applications).

- **DigitalInput_I1**: logiBUS digital input
    - **Type**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameters**: `QI = TRUE`, `Input = Input_I1`
    - **Explanation**: Reads the state of input `I1` and passes it on via the adapter `IN`. Serves as the trigger for the measurement request of the analog input.
- **DigitalOutput_Q1**: logiBUS digital output
    - **Type**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1`
    - **Explanation**: Switches the output `Q1` according to the state of `I1` (via `AX_SPLIT_2.OUT1`).
- **AnalogInput_I4**: logiBUS analog input
    - **Type**: `logiBUS::io::AI::logiBUS_AI_IDA`
    - **Parameters**: `QI = TRUE`, `Input = AnalogInput_I4`, `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Explanation**: Reads the analog input `I4` and provides the value via the adapter `IN`. A measurement can be requested via the adapter `SREQ`. The parameters are used for filtering (hysteresis, time delta, rate limit).
- **CALIBRATE**: Two-point calibration
    - **Type**: `adapter::Engineering::measurements::AR_CALIBRATE`
    - **Parameters**: `Y_Offset = 100.0`, `Y_Scale = 600.0`
    - **Explanation**: Calculates the calibrated value using the formula `Y = (X + OFFSET) * SCALE`. The event `CO` (Calibrate Offset) sets `OFFSET := Y_Offset - X`, the event `CS` (Calibrate Scale) sets `SCALE := Y_Scale / (X + OFFSET)`. `Y_Offset` and `Y_Scale` are target values on the output side. `CO` must be executed before `CS`, because `CS` uses the offset. The calculated values are output to / read from the bidirectional adapters `OFFSET` and `SCALE`. The calibrated output `Y` is not wired in this exercise.
- **INI_OFFSET**: Storage block for the offset
    - **Type**: `eclipse4diac::storage::INI_AR2`
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AR'`, `KEY = 'OFFSET'`, `DEFAULT_VALUE = 0.0`
    - **Explanation**: Loads the value from the INI file (section `Uebung_028a_AR`, key `OFFSET`; `DEFAULT_VALUE` applies if there is no entry) and provides it via the adapter `VAL`; new values from `VAL` are stored. `SETM = TRUE` enables mirroring on set.
- **INI_SCALE**: Storage block for the scale
    - **Type**: `eclipse4diac::storage::INI_AR2`
    - **Parameters**: `QI = TRUE`, `SETM = TRUE`, `SECTION = 'Uebung_028a_AR'`, `KEY = 'SCALE'`, `DEFAULT_VALUE = 1.0`
    - **Explanation**: Analogous to `INI_OFFSET`, but for the scale factor (key `SCALE`).
- **DigitalInput_I2_CO**: logiBUS input with click detection for the offset calibration
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: A single click on `I2` triggers `CALIBRATE.CO`.
- **DigitalInput_I3_CS**: logiBUS input with click detection for the scale calibration
    - **Type**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameters**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Explanation**: A single click on `I3` triggers `CALIBRATE.CS`.
- **AX_SPLIT_2**: Adapter splitter
    - **Type**: `adapter::events::unidirectional::AX_SPLIT_2`
    - **Parameters**: none
    - **Explanation**: Distributes the signal of `DigitalInput_I1` simultaneously to `DigitalOutput_Q1` (`OUT1`) and to the measurement request `AnalogInput_I4.SREQ` (`OUT2`).
- **AD_TO_AUDI**: Adapter converter
    - **Type**: `adapter::conversion::unidirectional::AD_TO_AUDI`
    - **Parameters**: none
    - **Explanation**: Converts the adapter supplied by the analog input into an `AUDI` adapter.
- **AUDI_TO_AR**: Adapter converter
    - **Type**: `adapter::conversion::unidirectional::AUDI_TO_AR`
    - **Parameters**: none
    - **Explanation**: Converts the `AUDI` adapter into the `AR` adapter that `AR_CALIBRATE` expects at its input `X`.

## Program Flow and Connections

1. `DigitalInput_I1.IN` → `AX_SPLIT_2.IN` (AdapterConnection): the signal of `I1` is fed into the splitter.
2. `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT` and `AX_SPLIT_2.OUT2` → `AnalogInput_I4.SREQ` (AdapterConnections): `Q1` follows `I1`, and at the same time a measurement is requested at the analog input.
3. `AnalogInput_I4.IN` → `AD_TO_AUDI.AD_IN` → `AUDI_TO_AR.AUDI_IN` (`AD_TO_AUDI.AUDI_OUT`) → `CALIBRATE.X` (`AUDI_TO_AR.AR_OUT`) (AdapterConnections): the measured value is passed to the calibration block with the correct type via the converter chain.
4. `DigitalInput_I2_CO.IND` → `CALIBRATE.CO` (EventConnection): a click on `I2` triggers the offset calibration.
5. `DigitalInput_I3_CS.IND` → `CALIBRATE.CS` (EventConnection): a click on `I3` triggers the scale calibration.
6. `CALIBRATE.OFFSET` → `INI_OFFSET.VAL` and `CALIBRATE.SCALE` → `INI_SCALE.VAL` (AdapterConnections): offset and scale are stored in the INI file and loaded from there at startup, so the calibration is retained permanently.

**Important note** (comment in the model): the double conversion is chosen deliberately. A direct `AD_TO_AR` would be like a "reinterpret_cast"; `AD_TO_AR_NUM` would work as well, though.

**Learning objectives of this exercise**:

- Handling analog input adapters and their parameterization.
- Using adapter converters for type adaptation.
- Using INI storage blocks to store configuration parameters permanently.
- Understanding the two-point calibration (offset, then scale).

## Summary

The exercise `Uebung_028a_AR` implements an analog input calibration in which offset and scale are taught in via two click inputs (`I2`, `I3`) and persisted in an INI file. A measurement is requested via `I1`, which at the same time switches `Q1`. The adapter converters `AD_TO_AUDI` and `AUDI_TO_AR` demonstrate the type-correct further processing of analog signals in the 4diac IDE.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
