# Exercise_028a2_AR: Analog Input Calibration with NVS Adapters

![Uebung_028a2_AR_network](./Uebung_028a2_AR_network.svg)

* * * * * * * * * *

## Introduction

This exercise implements the calibration of an analog input. An analog value is read, transferred to a calibration block via an adapter conversion, and the determined offset and scale values are permanently stored in the NVS (Non-Volatile Storage) memory. Two digital inputs control the calibration mode (offset and scale). Another digital input serves as a trigger for the analog sampling and is simultaneously routed to a digital output.

## Function Blocks (FBs) Used

| Name | Type | Parameters |
| ------ | ----- | ------------ |
| DigitalInput_I2 | `logiBUS::io::DI::logiBUS_IXA` | QI = TRUE, Input = Input_I2 |
| DigitalOutput_Q1 | `logiBUS::io::DQ::logiBUS_QXA` | QI = TRUE, Output = Output_Q1 |
| AnalogInput_I1 | `logiBUS::io::AI::logiBUS_AI_IDA` | QI = TRUE, Input = AnalogInput_I1, AnalogInput_hysteresis = 50, TimeDelta = 250, TimeRateLimit = 100 |
| CALIBRATE | `adapter::Engineering::measurements::AR_CALIBRATE` | Y_Offset = 100.0, Y_Scale = 600.0 |
| NVS_OFFSET | `logiBUS::storage::esp32_nvs::NVS_AR2` | QI = TRUE, SETM = TRUE, KEY = 'OFFSET', DEFAULT_VALUE = 0.0 |
| NVS_SCALE | `logiBUS::storage::esp32_nvs::NVS_AR2` | QI = TRUE, SETM = TRUE, KEY = 'SCALE', DEFAULT_VALUE = 1.0 |
| DigitalInput_I3_CO | `logiBUS::io::DI::logiBUS_IE` | QI = TRUE, Input = Input_I3, InputEvent = BUTTON_SINGLE_CLICK |
| DigitalInput_I4_CS | `logiBUS::io::DI::logiBUS_IE` | QI = TRUE, Input = Input_I4, InputEvent = BUTTON_SINGLE_CLICK |
| AX_SPLIT_2 | `adapter::events::unidirectional::AX_SPLIT_2` | (no parameters) |
| AD_TO_AR_NUM | `adapter::conversion::unidirectional::AD_TO_AR_NUM` | (no parameters) |

### Short Description of the Function Blocks

- **DigitalInput_I2**: Reads a digital input (Input_I2) and forwards the event via the adapter output `IN`.
- **DigitalOutput_Q1**: Outputs a digital signal to output `OUT` (Output_Q1) when the adapter input `OUT` is activated.
- **AnalogInput_I1**: Analog input block. It returns an analog value upon request (SREQ). Parameterized with hysteresis, time delta, and rate limiting.
- **CALIBRATE**: Performs the calibration. Receives the raw value at adapter `X` and the control signals `CO` (Calibrate Offset) and `CS` (Calibrate Scale). Outputs the calculated offset and scale via adapters `OFFSET` and `SCALE`.
- **NVS_OFFSET / NVS_SCALE**: Stores a floating-point value in non-volatile memory under the keys 'OFFSET' or 'SCALE' with predefined default values.
- **DigitalInput_I3_CO / DigitalInput_I4_CS**: Additional digital inputs for calibration control (Input_I3 = Offset calibration, Input_I4 = Scale calibration).
- **AX_SPLIT_2**: Distributes an incoming adapter event to two outputs (OUT1 and OUT2).
- **AD_TO_AR_NUM**: Instance of the type of the same name, `AD_TO_AR_NUM`. Converts the analog data adapter (`AD_IN`) numerically (AD → UDINT → REAL) to a real adapter (`AR_OUT`). A plain `AD_TO_AR` would instead act like a "reinterpret_cast" (bit pattern).

## Program Flow and Connections

The process is started by the digital input `Input_I2`:

1. **Event Distribution**: The event coming from `DigitalInput_I2` (adapter `IN`) is passed to `AX_SPLIT_2`. This splits the event:
    - **OUT1** → connected to `AnalogInput_I1.SREQ` → triggers the analog sampling.
    - **OUT2** → connected to `DigitalOutput_Q1.OUT` → the digital output `Output_Q1` is set.

2. **Analog Measurement Value**: After sampling, `AnalogInput_I1` outputs an analog data adapter via its output `IN`. This is passed to `AD_TO_AR_NUM.AD_IN`.

3. **Conversion**:
    - `AD_TO_AR_NUM` converts the analog data adapter (`AD_IN`) numerically into a real-value adapter (`AR_OUT`).
    - The real-value adapter is passed to the calibration input `CALIBRATE.X`.

4. **Calibration**: `DigitalInput_I3_CO` (`Input_I3`) is connected to `CALIBRATE.CO` and `DigitalInput_I4_CS` (`Input_I4`) to `CALIBRATE.CS`. Depending on the triggered control signal, `CALIBRATE` calculates the new offset or the new scale. The reference values (Y_Offset = 100.0, Y_Scale = 600.0) serve as the basis.

5. **Persistent Storage**:
    - The determined offset (adapter `OFFSET`) is transferred to `NVS_OFFSET.VAL` and stored under the key 'OFFSET'.
    - The determined scaling factor (adapter `SCALE`) is transferred to `NVS_SCALE.VAL` and stored under the key 'SCALE'.

This ensures that the calibration values are retained even after a controller restart.

## Summary

This exercise demonstrates a complete signal chain from digital triggering through analog acquisition, adapter conversion, and calibration to the permanent storage of the correction values in the NVS. It illustrates the use of multiple adapter types (event, data, and real adapters) and the interaction of standard FBs from the logiBUS library with special engineering blocks. The adapter conversion between analog data and real values is done with the dedicated block `AD_TO_AR_NUM` instead of a "reinterpret_cast".
