# InputOutputTesterButton_AI_Calibrate_2P_OPC_UA: AI Calibrate Tester (OPC-UA), 2-point

![InputOutputTesterButton_AI_Calibrate_2P_OPC_UA_network](./InputOutputTesterButton_AI_Calibrate_2P_OPC_UA_network.svg)

* * * * * * * * * *

## Introduction

`InputOutputTesterButton_AI_Calibrate_2P_OPC_UA` is the training example for **8 analog inputs with 2-point calibration** (zero/span), operable both via the ISOBUS Virtual Terminal and via OPC-UA (web client). Each channel shows the raw value, the calibrated value and a bargraph; calibration is triggered with two buttons (CO = zero point, CS = span). The 12 digital outputs are taken over unchanged from the [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) example.

Compared with its predecessor [`InputOutputTesterButton_AI_Calibrate_OPC_UA`](../Button_AI_Calibrate_OPC_UA/InputOutputTesterButton_AI_Calibrate_OPC_UA.md) the calibration logic was reworked: instead of `AR_CALIBRATE_SQ_REF` with OFFSET/SCALE, the channel block uses `AR_CALIBRATE_2P_REF`, which stores the two raw values (`X_LOW`/`X_HIGH`) and the two reference values (`LOW_REF`/`HIGH_REF`) independently of each other. A fixed order of CO before CS is therefore no longer required. Remote triggering via OPC-UA no longer uses toggle nodes (`AX_SUBSCRIBE`/`AX_RF_TRIG`) but argument-less OPC-UA **method calls** (`ID_CO_METHOD`/`ID_CS_METHOD`), which are passed on as an event directly to the calibration.

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `AnalogChannel_I1` … `AnalogChannel_I8` | [`MyLib::sys::logiBUS_AI_Calibrate_2P_IDA_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_AI_Calibrate_2P_IDA_OPC/) | `Input` = `AnalogInput_I1`…`I8`; `sSection` = `'AI_Calibrate_I1'`…`'AI_Calibrate_I8'`; VT objects `NumberVariable_AIC_RAW_I0n`, `NumberVariable_AIC_CAL_I0n_N`, `Button_AIC_I0n_CO`/`_CS`; OPC-UA IDs `AIC_In_RAW_WRITE`, `AIC_In_CAL_WRITE`, `AIC_In_CO_METHOD`, `AIC_In_CS_METHOD` | Analog input with 2-point calibration, VT display (raw, calibrated, bargraph) and OPC-UA connection |
| `Output_Q1` … `Output_Q12` | [`MyLib::sys::Button_IXA_TO_logiBUS_QXA_BG_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Button_IXA_TO_logiBUS_QXA_BG_OPC/) | `u16ObjId` = `Button_Q01`…`Q12`; `Output` = `Output_Q1`…`Q12`; `ID_READ`/`ID_WRITE` = `Q01_READ`/`Q01_WRITE` … | Digital output, unchanged from the DIDO example |
| `SystemTickSender` | [`MyLib::sys::SystemTickSender_ISO_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SystemTickSender_ISO_OPC/) | `u16ObjId` = `OutputNumber_Tick`; `ID_WRITE` = `Tick_WRITE` | Cyclic counter as a heartbeat, published to a VT number field and via OPC-UA |

### Additional parameters of the channel blocks

Each `AnalogChannel_In` also receives the reference values and raw-value diagnostics as VT objects and OPC-UA nodes:

- `stObjZero` = `NumberVariable_AIC_I0n_ZERO_N`, `stObjSpan` = `NumberVariable_AIC_I0n_SPAN_N`: VT input fields for the reference values (target value at the zero point and at the span).
- `ID_ZERO_READ`/`ID_ZERO_WRITE`, `ID_SPAN_READ`/`ID_SPAN_WRITE`: OPC-UA nodes for setting (subscribe) and echoing back (publish) the reference values, e.g. `AIC_I1_ZERO_READ`.
- `stObjZeroRaw` = `NumberVariable_AIC_I0n_ZERORAW_N`, `stObjSpanRaw` = `NumberVariable_AIC_I0n_SPANRAW_N` as well as `ID_ZERO_RAW_WRITE`/`ID_SPAN_RAW_WRITE`: display-only raw values stored at CO and CS (VT and OPC-UA).

### Inside the channel block

According to the library block `logiBUS_AI_Calibrate_2P_IDA_OPC` (version 1.x), a channel consists of:

- `InputRaw` (`logiBUS_AI_Raw_VT_AR_OPC`): reads the analog input, displays the raw value and publishes it.
- `CALIBRATE` (`AR_CALIBRATE_2P_REF`, `QI=TRUE`, `xClipping=FALSE`): the actual 2-point calibration with the event inputs `C_LOW`/`C_HIGH`.
- `TriggerCO`, `TriggerCS` (`Button_Method_IE`): trigger the calibration by the local VT button **or** the OPC-UA method call.
- `Y_OFFSET_LIT`, `Y_SCALE_LIT` (`NumericValue_TO_AR2_OPC`): supply the reference values from the VT input field or OPC-UA.
- `INI_X_LOW`, `INI_X_HIGH`, `INI_LOW_REF`, `INI_HIGH_REF` (`INI_AR2`): persist the four calibration values under `sSection` in the INI file so they survive a restart.
- `PublishCal`, `PublishZeroRaw`, `PublishSpanRaw` (`AR_TO_Q_Publish_OPC`): VT display and OPC-UA publish of the calibrated value and the raw values at the calibration points.

## Program Flow and Connections

The SubApp network contains **no** event or data connections between the instances: the 21 instances work independently of each other and are linked to the outside world solely through their parameters (VT object IDs, `AnalogInput_*`/`Output_*` constants, OPC-UA addresses).

1. **8 analog channels**: `AnalogChannel_I1`…`AnalogChannel_I8` read `AnalogInput_I1`…`AnalogInput_I8`. CO stores the current raw value as the zero point, CS as the span; triggered by VT button or OPC-UA method (`AIC_I*_CO_METHOD`/`AIC_I*_CS_METHOD`). Raw and calibrated values go to the VT and, via OPC-UA, to the outside (`AIC_I*_RAW_WRITE`/`AIC_I*_CAL_WRITE`).
2. **12 digital outputs**: `Output_Q1`…`Output_Q12`, unchanged from the DIDO example.
3. **Timer**: `SystemTickSender` feeds the VT number field `OutputNumber_Tick` and the OPC-UA node `Tick_WRITE`.

## Summary

`InputOutputTesterButton_AI_Calibrate_2P_OPC_UA` shows the 2-point calibration of 8 analog inputs with `AR_CALIBRATE_2P_REF`: independently stored raw and reference values (INI-persistent), CO/CS triggered either locally by VT button or by OPC-UA method call, reference values and raw-value diagnostics via VT and OPC-UA. The example uses only three block types with no connections in the network; the logic resides entirely in the channel block.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
