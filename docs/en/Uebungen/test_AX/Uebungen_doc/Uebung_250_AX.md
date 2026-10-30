# Uebung_250_AX: Measuring the Limit Frequency of the Analog Input

![Uebung_250_AX_network](./Uebung_250_AX_network.svg)

* * * * * * * * * *

## Introduction

An analog input (AI) does not deliver instantaneous values at an arbitrary rate. This exercise measures **up to which frequency an AI can still reproduce a square-wave signal correctly**. The same 0-10 V square wave is applied to `I1` (as AI, `logiBUS_AI_IDA`) and to `I2` (as PI, `logiBUS_PI_ID`) at the same time. The PI counts the pulses in hardware and thus provides the **reference frequency**. From the AI values, the frequency is reconstructed with a Schmitt trigger and an edge counter. Both frequencies are compared per second of gate time.

**Firmware background (`IOHandleESP32AnalogIN.cpp`):** The ESP32-P4 reads the ADC in continuous mode at 20 kHz in total. For each read, 64 samples (about 3.2 ms) per channel are averaged; only this average is reported to 4diac. That gives roughly 300 AI values per second. Above a certain signal frequency, the driver averages the square wave to a value around 50 %, the Schmitt trigger no longer switches and the AI frequency drops.

> **Measurement result (machine, 2026-10-30):** The limit frequency is between **90 and 100 Hz**. Up to 90 Hz the AI counts exactly, from 100 Hz it loses edges. For frequency measurements above about 90 Hz, the PI input is mandatory. See the section "Measurement Result" for details.

> **Prerequisite:** The exercise needs a device on which `I1` and `I2` are not occupied by another resource. If another resource runs in parallel (e.g. a boot file in `EMB_RES`), `EMB_RES_AX` gets no handle: all values stay 0 and `OK` is still TRUE. The measurement only worked after the other resource was emptied.

## Function Blocks Used (FBs)

- **AI**: analog input I1 (type: `logiBUS::io::AI::logiBUS_AI_IDA`)
    - **Parameters**: QI = TRUE, Input = AnalogInput_I1, AnalogInput_hysteresis = 1, TimeDelta = 1, TimeRateLimit = 0
    - **Explanation**: Reports every ADC averaging. With TimeRateLimit = 0 the rate limit is switched off (the default of 100 ms would already limit at about 5 Hz).
- **AD_SPLIT_2**: distributes the AI value to two branches (type: `adapter::events::unidirectional::AD_SPLIT_2`)
- **AD_TO_AR_NUM**: DWORD → REAL, numerically correct (not the bit reinterpretation, see `Uebung_237_AX`)
- **SCHMITT**: Schmitt trigger (type: `logiBUS::signalprocessing::hysteresis::Hysteresis_AR_AX`)
    - **Explanation**: On from 3072 (75 %), off below 1024 (25 %). Threshold and hysteresis come from the two constants `LIT_THRESHOLD` and `LIT_HYSTERESIS` (2048.0 each).
- **R_TRIG_AI**: rising edge (type: `AX_R_TRIG`) → **AI_EDGES** (`E_CTU`): counts the edges per gate time.
- **AD_D_TO_DWORD** → **AI_SAMPLES** (`E_CTU`): counts all AI values per gate time (effective sampling rate).
- **GATE**: `E_CYCLE`, DT = T#1s - the gate time. Started by `AI.INITO`.
- **F_AI**, **SAMPLES_S**: `F_UINT_TO_REAL`, provide edges and AI values per second (= Hz and 1/s).
- **PI**: pulse input I2 (type: `logiBUS::io::PI::logiBUS_PI_ID`), ImpulseDelta = 0, TimeDelta = 250, TimeRateLimit = 100. Hardware counter as reference.
- **PI_UDINT** (`F_DWORD_TO_UDINT`) → **PI_NOW** (`F_UDINT_TO_REAL`), **PI_PREV** (counter value of the previous second), **F_PI_SUB** (`F_SUB`), **F_PI** (`F_MOVE`): the difference of the counter values = `f_PI` in Hz.
    - **Note:** deliberately via `F_DWORD_TO_UDINT` + `F_UDINT_TO_REAL`, not `F_DWORD_TO_REAL` (bit trap, see `Uebung_237_AX`).
- **DIFF_SUB**, **DIFF**, **DIFF_ABS**, **ABW**: deviation `|f_AI − f_PI|`.
- **OK**: `F_LE`, IN2 = REAL#2.0 - TRUE as long as the deviation is at most 2 Hz.
- **PERMIT_OK** (`E_PERMIT`), **GRENZ_MAX** (`F_MAX`), **F_GRENZ** (`F_MOVE`): remembers the **highest `f_PI` at which `OK` was TRUE** = measured limit frequency.
    - **Note:** The `F_MOVE` blocks with `DataType = REAL` serve as type anchors behind the generic blocks `F_SUB`, `F_ABS` and `F_MAX`.

### Sub-blocks: none

## Program Flow and Connections

1. Every AI value passes through `AD_SPLIT_2` into two branches: the first goes via `AD_TO_AR_NUM` into `SCHMITT`, whose rising edges (`R_TRIG_AI`) are counted in `AI_EDGES`. The second counts every AI value in `AI_SAMPLES`.
2. `GATE` fires every second: `F_AI` reads `AI_EDGES.CV`, then `SAMPLES_S` reads `AI_SAMPLES.CV`. Afterwards both counters are reset.
3. The same chain requests the PI (`PI.REQ`). The difference to the counter value of the previous second gives `f_PI`.
4. `F_AI` and `F_PI` are compared: `ABW = |f_AI − f_PI|`, `OK = (ABW ≤ 2 Hz)`.
5. Only if `OK = TRUE` does `PERMIT_OK` pass on, and `F_GRENZ` takes the maximum of `f_PI` and its previous value.

## Operation

1. In the `Control` of `test_AX.sys`, use *Change Type* to switch to `Uebungen::Uebung_250_AX`.
2. Set watches on `F_AI.OUT`, `F_PI.OUT`, `SAMPLES_S.OUT`, `ABW.OUT`, `OK.OUT`, `F_GRENZ.OUT`.
3. Raise the generator frequency slowly. As long as `OK.OUT` stays TRUE, the AI follows the signal. `F_GRENZ.OUT` shows the highest frequency so far with `OK`.
4. For a new measurement, restart the program.

**Notes:** The first gate-time value after start is unreliable. In the limit range (about 90-100 Hz) `F_GRENZ` is not reliable, because a single "lucky" second is enough. It is still unchecked whether `PI.REQ` reads the hardware counter freshly.

## Measurement Result

Square wave 0-10 V on `I1` (AI) and `I2` (PI, reference), gate time 1 s:

| f (Hz) | F_AI | F_PI | SAMPLES_S (AI values/s) | Values per period | OK |
|-------:|-----:|-----:|------------------------:|------------------:|----|
| 1 | 1 | 1 | 132 | - | TRUE |
| 10 | 10 | 10 | 196 | 19.6 | TRUE |
| 30 | 30 | 30 | 252 | 8.4 | TRUE |
| 50 | 50 | 50 | 286 | 5.7 | TRUE |
| 60 | 60 | 60 | 287 | 4.8 | TRUE |
| 70 | 70 | 70 | 303 | 4.3 | TRUE |
| 80 | 80 | 80 | 312 | 3.9 | TRUE |
| 90 | 90 | 90 | 313 | 3.5 | TRUE |
| 100 | 87 | 100 | 312 | 3.1 | FALSE (ABW 13) |
| 110 | 81 | 110 | 312 | 2.8 | FALSE (ABW 29) |

- **Up to 90 Hz** the AI counts exactly. **From 100 Hz** it loses edges, above that aliasing occurs: the detected frequency **drops** although the signal frequency rises.
- The AI rate is not fixed. With a slow signal fewer reports arrive (hysteresis 1, changes only). Its upper limit is about 312 per second, i.e. one average every 3.2 ms.
- At about 3 values per period the averaging smears the edges and the Schmitt thresholds (75 % / 25 %) are no longer reached reliably.
- In the limit range the number of detected edges varies from second to second. `F_GRENZ` showed 100 Hz because in one second all edges happened to be detected. About 90 Hz is practically usable.

## Market Comparison

Comparison with common small controllers. The competitor values come from the manufacturer manuals ([LOGO! 8 system manual](https://cache.industry.siemens.com/dl/files/041/109741041/att_924627/v1/logo_system_manual_de-DE_de-DE.pdf), [LOGO! product information 05/2024](https://cache.industry.siemens.com/dl/files/848/109971848/att_1286527/v1/Product_Information_for_LOGO_System_Manual_de-DE_de-DE.pdf), [easyE4 manual MN050009](https://es-assets.eaton.com/DOCUMENTATION/AWB_MANUALS/MN050009_EN.pdf)), the logiBUS hardware values from our own measurements.

### Analog Input (0-10 V)

| | logiBUS (ESP32-P4) | Siemens LOGO! 8 | Eaton easyE4 |
|---|---|---|---|
| Resolution | 12 bit | Base module: not specified (error limit ±1.5 %); AM2: 10 bit, normalized to 0-1000 | 12 bit (0-4095) |
| Update | up to about 312 values/s (every 3.2 ms) | Base module 300 ms, AM2 50 ms | adjustable in easySoft; no fixed figure found |
| Filter | Averaging in the driver (64 samples) | Analog filter and average function blocks | Noise suppression and averaging adjustable, PT1 filter block |
| Square wave measured cleanly up to | about 90 Hz (measurement 2026-10-30) | not specified | not specified |

### Pulse Input

| | Input | Square wave 12 V, measurement 2026-08-30 | Comparison |
|---|---|---|---|
| logiBUS, fast PI | `I1`, `I2` (4.7 kΩ + 100 pF) | good up to 20 kHz, no longer good at 23 kHz | LOGO! 8: max. 5 kHz on `I3`-`I6` (12/24RCE/RCEo and 24CE/24CEo only, directly at the counter) |
| logiBUS, slow PI | `I3`-`I8` (4.7 kΩ + 100 nF) | 2.3 kHz | easyE4: counter and frequency meter max. 5 kHz |

**Notes on the comparison**

- The fast PI at 20 kHz is about four times the 5 kHz of both small controllers. The slow PI is deliberately designed for slower, analog-type signals by its 100 nF filter and is not compared with the competitors' fast counter inputs.
- The competitors state the maximum of the counter function. For logiBUS it is the frequency up to which the input still counts cleanly; above it the input loses edges but is not damaged.
- For the analog input, neither manufacturer specifies a usable signal frequency. On the LOGO! 8, the analog value cycle time (300 ms or 50 ms) limits sampling much more strongly than on logiBUS.
- The easyE4 data sheets state conversion "each CPU cycle"; the manual only says the update rate is adjustable. One device data sheet gives 10 kHz counting frequency, the manual 5 kHz; this is not resolved.
- PI test setup: FNIRSI SG-003A in Pulse mode (Level, 12.0 V peak), one measurement per input, frequency read at the generator and in the 4diac program. The generator's output impedance and duty cycle are not documented. Filter values from the parts list `APIXON Node-ISO 20 v107`.

## Summary

The exercise determines the limit frequency of the analog input experimentally by comparing the frequency reconstructed from the averaged AI values with the hardware count of the PI. It shows that the averaging in the driver (about 3.2 ms per value) limits the usable signal frequency, and that fast signals must be captured with a pulse input rather than an analog input.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
