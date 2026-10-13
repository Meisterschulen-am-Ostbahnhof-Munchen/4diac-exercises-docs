# InputOutputTesterButton_AI_Calibrate_3P_OPC_UA: AI Calibrate Tester (OPC-UA), 3-point

![InputOutputTesterButton_AI_Calibrate_3P_OPC_UA_network](./InputOutputTesterButton_AI_Calibrate_3P_OPC_UA_network.svg)

* * * * * * * * * *

## Introduction

`InputOutputTesterButton_AI_Calibrate_3P_OPC_UA` is the training example for **8 analog inputs with 3-point calibration** (minimum/middle/maximum), intended for sensors with a center position, e.g. a steering-angle sensor (left/straight/right). It is operable via the ISOBUS Virtual Terminal and via OPC-UA (web client). Each channel shows the raw value, the calibrated value and a bargraph; calibration uses three buttons (C_MIN, C_MID, C_MAX). The 12 digital outputs are taken over unchanged from the [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) example.

The example is the 3-point counterpart of [`InputOutputTesterButton_AI_Calibrate_2P_OPC_UA`](../Button_AI_Calibrate_2P_OPC_UA/InputOutputTesterButton_AI_Calibrate_2P_OPC_UA.md) (predecessor: [`InputOutputTesterButton_AI_Calibrate_OPC_UA`](../Button_AI_Calibrate_OPC_UA/InputOutputTesterButton_AI_Calibrate_OPC_UA.md)): the channel block uses `AR_CALIBRATE_3P_REF` with three independently stored raw values (`X_MIN`/`X_MID`/`X_MAX`) and three reference values (`MIN_REF`/`MID_REF`/`MAX_REF`). All three buttons can also be triggered as argument-less OPC-UA **method calls** (`ID_MIN_METHOD`/`ID_MID_TRIGGER_METHOD`/`ID_MAX_METHOD`).

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `AnalogChannel_I1` … `AnalogChannel_I8` | [`MyLib::sys::logiBUS_AI_Calibrate_3P_IDA_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_AI_Calibrate_3P_IDA_OPC/) | `Input` = `AnalogInput_I1`…`I8`; `sSection` = `'AI_Calibrate_3P_I1'`…`'AI_Calibrate_3P_I8'`; VT objects `NumberVariable_AIC_RAW_I0n`, `NumberVariable_AIC_CAL_I0n_N`; buttons `u16ObjId_C_MIN` = `Button_AIC_I0n_CO`, `u16ObjId_C_MID` = `Button_AIC_I0n_MID`, `u16ObjId_C_MAX` = `Button_AIC_I0n_CS`; OPC-UA IDs `AIC_In_RAW_WRITE`, `AIC_In_CAL_WRITE`, `AIC_In_MIN_METHOD`, `AIC_In_MID_METHOD`, `AIC_In_MAX_METHOD` | Analog input with 3-point calibration, VT display (raw, calibrated, bargraph) and OPC-UA connection |
| `Output_Q1` … `Output_Q12` | [`MyLib::sys::Button_IXA_TO_logiBUS_QXA_BG_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Button_IXA_TO_logiBUS_QXA_BG_OPC/) | `u16ObjId` = `Button_Q01`…`Q12`; `Output` = `Output_Q1`…`Q12`; `ID_READ`/`ID_WRITE` = `Q01_READ`/`Q01_WRITE` … | Digital output, unchanged from the DIDO example |
| `SystemTickSender` | [`MyLib::sys::SystemTickSender_ISO_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SystemTickSender_ISO_OPC/) | `u16ObjId` = `OutputNumber_Tick`; `ID_WRITE` = `Tick_WRITE` | Cyclic counter as a heartbeat, published to a VT number field and via OPC-UA |

### Additional parameters of the channel blocks

- `stObjMinRef` = `NumberVariable_AIC_I0n_ZERO_N`, `stObjMidRef` = `NumberVariable_AIC_I0n_MID_N`, `stObjMaxRef` = `NumberVariable_AIC_I0n_SPAN_N`: VT input fields for the three reference values.
- `ID_MINREF_READ`/`_WRITE`, `ID_MIDREF_READ`/`_WRITE`, `ID_MAXREF_READ`/`_WRITE` (e.g. `AIC_I1_MINREF_READ`): OPC-UA nodes for setting (subscribe) and echoing back (publish) the reference values.
- `stObjMinRaw`/`stObjMidRaw`/`stObjMaxRaw` = `NumberVariable_AIC_I0n_MINRAW_N`/`_MIDRAW_N`/`_MAXRAW_N` (plus one `ID_*_RAW_WRITE` node each): display-only raw values stored at the three calibration points.

### Inside the channel block

According to the library block `logiBUS_AI_Calibrate_3P_IDA_OPC`:

- `InputRaw` (`logiBUS_AI_Raw_VT_AR_OPC`): reads the analog input, displays the raw value and publishes it.
- `CALIBRATE` (`AR_CALIBRATE_3P_REF`, `QI=TRUE`, `xClipping=FALSE`): 3-point calibration with the event inputs `C_MIN`/`C_MID`/`C_MAX`.
- `TriggerMIN`, `TriggerMID`, `TriggerMAX` (`Button_Method_IE`): trigger via local VT button **or** OPC-UA method call; all three are identical in construction.
- `MIN_REF_LIT`, `MID_REF_LIT`, `MAX_REF_LIT` (`NumericValue_TO_AR2_OPC`): reference values from the VT input field or OPC-UA.
- `INI_X_MIN`/`INI_X_MID`/`INI_X_MAX` (default `0.0`) and `INI_MIN_REF`/`INI_MID_REF`/`INI_MAX_REF` (defaults `-100.0`/`0.0`/`100.0`), all `INI_AR2`: persist the six calibration values under `sSection`.
- `PublishCal`, `PublishMinRaw`, `PublishMidRaw`, `PublishMaxRaw` (`AR_TO_Q_Publish_OPC`): VT display and OPC-UA publish.

## Program Flow and Connections

The SubApp network contains **no** event or data connections between the instances: the 21 instances work independently of each other and are linked to the outside world solely through their parameters (VT object IDs, `AnalogInput_*`/`Output_*` constants, OPC-UA addresses).

1. **8 analog channels**: `AnalogChannel_I1`…`AnalogChannel_I8` read `AnalogInput_I1`…`AnalogInput_I8`. The user moves the sensor to minimum, middle and maximum and presses C_MIN, C_MID or C_MAX respectively (VT button or OPC-UA method `AIC_I*_MIN_METHOD`/`_MID_METHOD`/`_MAX_METHOD`). Raw and calibrated values go to the VT and, via OPC-UA, to the outside (`AIC_I*_RAW_WRITE`/`AIC_I*_CAL_WRITE`).
2. **12 digital outputs**: `Output_Q1`…`Output_Q12`, unchanged from the DIDO example.
3. **Timer**: `SystemTickSender` feeds the VT number field `OutputNumber_Tick` and the OPC-UA node `Tick_WRITE`.

## Summary

`InputOutputTesterButton_AI_Calibrate_3P_OPC_UA` shows the 3-point calibration of 8 analog inputs with `AR_CALIBRATE_3P_REF` for sensors with a center position: three independently stored raw and reference values (INI-persistent), triggered either by VT button or OPC-UA method call, reference values and raw-value diagnostics via VT and OPC-UA. The network contains no connections; the logic resides entirely in the channel block.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
