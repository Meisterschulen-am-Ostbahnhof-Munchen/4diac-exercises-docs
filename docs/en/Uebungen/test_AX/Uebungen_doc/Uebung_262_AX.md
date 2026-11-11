# Uebung_262_AX: Counting RAMPO with gamma

![Uebung_262_AX_network](./Uebung_262_AX_network.svg)

* * * * * * * * * *

## Introduction

Each PWM block reports with the event `RAMPO` that the new value is reached (see [`logiBUS_QDA_PWM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QDA_PWM/)). This exercise counts the `RAMPO` events with one `E_CTU` each; the counter value `CV` is read with a watch. This shows how often the ramp actually finishes when the potentiometer is adjusted. Outputs: `Q1` linear and `Q2` with gamma 2.2, both `RAMP = T#2s`.

**Prerequisite (once):** The exercise uses `logiBUS_AI_Calibrate_2P_OPC`. Insert `Button_AI_Calibrate_2P_OPC_UA` as `Control` in `test_AX.sys`, export the boot file and calibrate with the web client `apixon-ai-calibrate-2p.html` (0 %: `CO`, 100 %: `CS`). The INI file (section `AI_Calibrate_I1`) stays on the board. After that the exercise runs without a VT and without a dashboard.

## Function Blocks Used (FBs)

- **AnalogInput_I1_Cal**: analog input I1 with two-point calibration via OPC UA (type: `logiBUS_AI_Calibrate_2P_OPC`, section `AI_Calibrate_I1`)
    - **Explanation**: `CAL_OUT` delivers the calibrated value 0 to 100 %. `INITO` starts the PWM blocks.
- **AR_MUL_Scale** (`AR_MUL_2`) with **initval_Scale** (`initval_AR`, factor `81.91`): converts the value 0 to 100 % into the target range.
- **AR_MIN_Max** (`AR_MIN`) with **initval_Max** (`8191.0`): limits upwards.
- **AR_MAX_Null** (`AR_MAX`) with **initval_Null** (`0`): limits downwards. Together with `AR_MIN_Max` the value stays in the range 0 to 8191.0.
- **AR_TO_AD_NUM** (`AR_TO_AD_NUM`): converts REAL **numerically** to DWORD and passes the value on through the adapter.
- **AD_SPLIT** (`AD_SPLIT_2`): distributes the value to 2 PWM outputs.
- **PWMOutput_Q1_linear** (type: `logiBUS_QDA_PWM`, `QI = TRUE`, `Output = Output_Q1`, `RAMP = T#2s`)
    - **Explanation**: linear duty cycle at `Q1`.
- **PWMOutput_Q2_gamma** (type: `logiBUS_QDA_PWM_G`, `QI = TRUE`, `Output = Output_Q2`, `RAMP = T#2s`, `GAMMA = 2.2`)
    - **Explanation**: gamma-corrected duty cycle at `Q2`.
- **RAMPO_Count_Q1**, **RAMPO_Count_Q2** (`E_CTU`, `PV = 65535`): count the `RAMPO` events of `Q1` and `Q2` respectively. `CV` is the counter value.

### Sub-blocks: none

## Program Flow and Connections

1. The potentiometer value runs from `AnalogInput_I1_Cal.CAL_OUT` through `AR_MUL_Scale`, `AR_MIN_Max` and `AR_MAX_Null` to `AR_TO_AD_NUM` (0 to 8191). `AD_SPLIT` distributes the value to `PWMOutput_Q1_linear` and `PWMOutput_Q2_gamma`.
2. `AnalogInput_I1_Cal.INITO` initializes both PWM blocks.
3. `PWMOutput_Q1_linear.RAMPO` goes to `RAMPO_Count_Q1.CU`, `PWMOutput_Q2_gamma.RAMPO` to `RAMPO_Count_Q2.CU`.

## Operation

1. Set the exercise `Uebungen::Uebung_262_AX` in the `Control` and deploy it to the board (or install its boot file). Otherwise the previously loaded calibration application keeps running and the counters are not available.
2. Set watches on `RAMPO_Count_Q1.CV` and `RAMPO_Count_Q2.CV`.
3. Turn the potentiometer at `I1` once and read the counter values afterwards. With further adjustments compare how the two counters develop: both outputs have `RAMP = T#2s`; the difference is the gamma correction at `Q2`. So the comparison is linear versus gamma.

## Summary

The exercise makes the event `RAMPO` measurable: one counter per output shows how often the ramp is reported as finished.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
