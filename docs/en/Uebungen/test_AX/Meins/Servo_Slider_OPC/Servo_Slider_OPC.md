# Servo_Slider_OPC: Servo via Web Slider (OPC-UA)

![Servo_Slider_OPC_network](./Servo_Slider_OPC_network.svg)

* * * * * * * * * *

## Introduction

`Servo_Slider_OPC` is a demo: a servo on `Output_Q2` is set via a web slider over **OPC-UA** to an angle of **0 to 180 degrees**; the angle actually used (clamped) is published back via OPC-UA as an echo. The SubApp interface is empty.

The two OPC-UA connections are defined in `SubStrings.gcf` as WSTRING constants:

| Constant | Value | Direction |
|---|---|---|
| `SERVO_Q2_ANGLE_SET` | `opc_ua[READ;/Objects/Servo/Q02/ANGLE_SET,1:s=SERVO_Q2_ANGLE_SET]` | Read target angle from the slider |
| `SERVO_Q2_ANGLE` | `opc_ua[WRITE;/Objects/Servo/Q02/ANGLE,1:s=SERVO_Q2_ANGLE]` | Write the angle echo |

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `AngleSubscribe` | [AR_SUBSCRIBE_1](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AR_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SERVO_Q2_ANGLE_SET` | Reads the target angle via OPC-UA |
| `AngleToReal` | [AR_R_TO_REAL](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/REAL/AR_R_TO_REAL/) | – | Converts the adapter value to a `REAL` |
| `Clamp` | [F_ClampReal](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/clamp/F_ClampReal/) | `rMin` = `0.0`, `rMax` = `180.0` | Limits the angle to 0…180 |
| `ToFraction` | [F_DIV](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_DIV/) | `IN2` = `REAL#180.0` | Divides the angle by 180 (fraction 0…1) |
| `ToWord` | [FIELDBUS_PERCENT_TO_WORD](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/signalprocessing/FIELDBUS_PERCENT_TO_WORD/) | – | Converts the fraction to a word (servo setpoint) |
| `AngleToAW` | [AW_WORD_TO_W](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/WORD/AW_WORD_TO_W/) | – | Passes the word to the WORD adapter of the servo output |
| `ServoQ2` | [logiBUS_QWA_SERVO](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO-Servo/logiBUS_QWA_SERVO/) | `QI` = `TRUE`, `Output` = `Output_Q2` | Servo output Q2 |
| `AngleEchoToAR` | [AR_REAL_TO_R](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/REAL/AR_REAL_TO_R/) | – | Converts the clamped angle to an AR adapter value |
| `AngleEcho` | [AR_PUBLISH_1](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/net/AR_PUBLISH_1/) | `QI` = `TRUE`, `ID` = `SERVO_Q2_ANGLE` | Publishes the angle via OPC-UA |

## Program Flow and Connections

- **Initialization**: `AngleSubscribe.INITO` → `AngleEcho.INIT`, then `AngleEcho.INITO` → `ServoQ2.INIT`.
- **Setpoint**: the subscribe result (`AngleSubscribe.OUT`) runs through `AngleToReal` → `Clamp` → `ToFraction` → `ToWord` → `AngleToAW` to `ServoQ2.OUT`. Each step hands over to the next via its `CNF` event (`AngleToReal.CNF` → `Clamp.REQ` → `ToFraction.REQ` → `ToWord.REQ` → `AngleToAW.REQ`).
- **Echo**: additionally, `Clamp.CNF` triggers `AngleEchoToAR`; it feeds the clamped angle into `AngleEcho`, which publishes it via OPC-UA. The web client therefore sees the angle actually output, limited to 0…180.

The normalization (angle divided by 180, then fraction-to-word conversion) maps the range 0…180 degrees onto the full setpoint range of the servo output.

## Summary

`Servo_Slider_OPC` shows a chain "read OPC-UA → clamp → normalize → servo output" with a parallel echo branch that reports the clamped value back via OPC-UA.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
