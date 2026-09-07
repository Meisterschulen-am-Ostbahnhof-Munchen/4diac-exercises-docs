# Exercise_237_AX: AD_TO_AR Bit Trap – Bit Reinterpretation vs. Numerical Cast

![Uebung_237_AX_network](./Uebung_237_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is deliberately designed as a comparison between a **wrong** and a **right** solution: The same raw analog value is converted from `AD` (DWORD adapter) to `AR` (REAL adapter) in two ways – once incorrectly via `AD_TO_AR` (bit reinterpretation) and once correctly via `AD_TO_AR_NUM` (true numerical cast). The instance names `AD_TO_AR_WRONG` and `AD_TO_AR_NUM_CORRECT` were deliberately chosen to highlight the pitfalls of converting analog values.

## Function Blocks (FBs) Used

- **AnalogInput_I4**: Analog input (Type: `logiBUS::io::AI::logiBUS_AI_IDA`)

- **Parameters**: QI = TRUE, Input = AnalogInput_I4, AnalogInput_hysteresis = 50, TimeDelta = 250, TimeRateLimit = 100

- **Explanation**: Reads the raw analog measurement value as a `AD` adapter (DWORD).

- **AD_SPLIT_2**: Splits an AD signal to two destinations (Type: `adapter::events::unidirectional::AD_SPLIT_2`)

- **Parameters**: None

- **Explanation**: Duplicates the single raw analog value losslessly so that it can be observed in parallel via both conversion paths.

- **AD_TO_AR_WRONG**: Conversion from AD to AR – FALSE (Type: `adapter::conversion::unidirectional::AD_TO_AR`)

- **Parameters**: None

- **Explanation**: Looks like the obvious direct conversion, but internally it's `F_DWORD_TO_REAL` – a pure IEEE 754-bit reinterpretation. A raw analog value like `DWORD#2048` is **not** converted to `REAL#2048.0`, but rather to a meaningless number close to zero.

- **AD_TO_AR_NUM_CORRECT**: Conversion from AD to AR – CORRECT (Type: `adapter::conversion::unidirectional::AD_TO_AR_NUM`)

- **Parameters**: None

- **Explanation**: Performs the same task numerically correctly, via the intermediate chain DWORD → UDINT → REAL – the same chain that manually assembles `Uebung_028a_AR` from `AD_TO_AUDI` + `AUDI_TO_AR`. `AD_TO_AR_NUM` is the drop-in compatible single-block equivalent.

### Sub-Blocks: None

This exercise does not use any further sub-blocks; all function blocks are located directly at the top level of the sub-app.

## Program Flow and Connections

1. `AD_SPLIT_2.IN` ← `AnalogInput_I4.IN`: The raw analog value is distributed to two consumers.

2. `AD_TO_AR_WRONG.AD_IN` ← `AD_SPLIT_2.OUT1`: The first branch converts via bit reinterpretation (`F_DWORD_TO_REAL`) – the result is a meaningless number close to zero.

3. `AD_TO_AR_NUM_CORRECT.AD_IN` ← `AD_SPLIT_2.OUT2`: The second branch converts numerically (DWORD → UDINT → REAL) – the result corresponds to the actual measured value.

4. **Task**: Observe both `AR_OUT.D1` pins in the running 4diac monitor (right-click on the pin → Watch) and compare them: `AD_TO_AR_WRONG` displays a small number that appears unrelated to the actual analog value; `AD_TO_AR_NUM_CORRECT` displays the plausible, actual measured value.

**When is `AD_TO_AR` correct anyway?** If `AD_IN` is already a bit pattern that was intended to be REAL (e.g., the result of `F_REAL_TO_DWORD` elsewhere) – then bit reinterpretation is exactly the correct, lossless reverse. A raw analog/counter value, as in this exercise, is the counterexample: Here, the numerical cast (`AD_TO_AR_NUM`) is correct.

## Summary

Exercise 237_AX demonstrates, using two parallel conversion paths, that `AD_TO_AR` and `AD_TO_AR_NUM` are fundamentally different operations: `AD_TO_AR` is a pure bit reinterpretation (correct only if the input is already an intentionally encoded REAL bit pattern), while `AD_TO_AR_NUM` is the numerically correct cast for raw analog or counter values. Anyone who selects the seemingly "direct" function block `AD_TO_AR` when converting an analog value will get a plausible-looking but incorrect result – this exercise trains you to be aware of this potential for confusion.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de ](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
