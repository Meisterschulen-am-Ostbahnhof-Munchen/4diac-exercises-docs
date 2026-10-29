# Uebung_250_AX: Measuring the Limit Frequency of the Analog Input

![Uebung_250_AX_network](./Uebung_250_AX_network.svg)

* * * * * * * * * *

## Introduction

An analog input (AI) does not deliver instantaneous values at an arbitrary rate. This exercise measures **up to which frequency an AI can still reproduce a square-wave signal correctly**. The same 0-10 V square wave is applied to `I1` (as AI, `logiBUS_AI_IDA`) and to `I2` (as PI, `logiBUS_PI_ID`) at the same time. The PI counts the pulses in hardware and thus provides the **reference frequency**. From the AI values, the frequency is reconstructed with a Schmitt trigger and an edge counter. Both frequencies are compared per second of gate time.

**Firmware background (`IOHandleESP32AnalogIN.cpp`):** The ESP32-P4 reads the ADC in continuous mode at 20 kHz in total. For each read, 64 samples (about 3.2 ms) per channel are averaged; only this average is reported to 4diac. That gives roughly 300 AI values per second. Above a certain signal frequency, the driver averages the square wave to a value around 50 %, the Schmitt trigger no longer switches and the AI frequency drops.

> The expected limit frequency (roughly 100-150 Hz) is an estimate. Measurement results from the machine are still pending.

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

**Notes:** The first gate-time value after start is unreliable. It is still unchecked whether `PI.REQ` reads the hardware counter freshly.

## Summary

The exercise determines the limit frequency of the analog input experimentally by comparing the frequency reconstructed from the averaged AI values with the hardware count of the PI. It shows that the averaging in the driver (about 3.2 ms per value) limits the usable signal frequency, and that fast signals must be captured with a pulse input rather than an analog input.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
