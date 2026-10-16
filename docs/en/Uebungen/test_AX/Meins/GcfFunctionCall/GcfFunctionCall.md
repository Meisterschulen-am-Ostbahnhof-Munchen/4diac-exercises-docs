# GcfFunctionCall: Calling a Function Written in ST from a GCF

* * * * * * * * * *

## Introduction

This exercise in the package `Meins::GcfFunctionCall` shows how a function written in Structured Text (`.fct`) can be used from a GlobalConstants file (`.gcf`). It is the basis for a later library of its own (analogous to `MyLib_AX_Option`).

## Contents

| File | Purpose |
|---|---|
| `F_Double.fct` | ST function, `F_Double(Value) = Value * 2` (INT) |
| `const/FuncConst.gcf` | `BASE_VALUE := 7`, `DOUBLE_VALUE := F_Double(BASE_VALUE)` |
| [`GcfFunctionCallTest`](GcfFunctionCallTest.md) | Basic FB: reads `DOUBLE_VALUE` and calls `F_Double` directly; `Passed` is TRUE when both give 14 |
| [`Uebung_011d_GcfFunc_AX`](Uebung_011d_GcfFunc_AX.md) | VT display: `DOUBLE_VALUE` as `rPhys` to `Q_NumericValue_PHYS` (`OutputNumber_N3_N`) |

## Program Flow and Connections

1. `FuncConst.gcf` imports nothing. The function is called by its full name `Meins::GcfFunctionCall::F_Double(...)` in the `InitialValue`.
2. `Uebung_011d_GcfFunc_AX` imports `DOUBLE_VALUE` and `OutputNumber_N3_N` (from `Uebungen::const::UT::DefaultPool_Numeric`) and sets them as the parameters `rPhys` and `stObj` of the `Q_NumericValue` block.
3. Expectation: N3 shows 14 (offset and scale of the object act on the value).

## Result

The export accepts the function call as an `InitialValue` and evaluates it at export time. In the boot file `test_AX_FORTE_PC_AX.fboot`, `rPhys` is directly `14.0` (= `F_Double(7)`); neither `F_Double` nor `DOUBLE_VALUE` appear there. The function therefore does not run on the device at runtime. Precondition: the arguments are constants.

Not verified: the display of 14 on N3 in the VT (offset and scale of the object).

## Notes

- Keep `Comment=` in the GCF short (IDE wrap bug on resave).
- `Uebung_011d.SUB` had the same name as `Uebungen/Uebung_011d_AX` and `_AUDI` (different package, same base). To avoid confusion it is now called `Uebung_011d_GcfFunc_AX`.

## Summary

Function calls with constant arguments can be used in a GCF as initial values. The value is computed at export time and ends up as a constant in the boot file.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
