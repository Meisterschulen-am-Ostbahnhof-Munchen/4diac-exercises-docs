# GcfFunctionCallTest: Test for the Function from the GCF

![GcfFunctionCallTest](GcfFunctionCallTest.svg)

* * * * * * * * * *

## Introduction

`GcfFunctionCallTest` is a basic FB in the package `Meins::GcfFunctionCall`. It compares the value `DOUBLE_VALUE` from the GCF with a direct call of the ST function `F_Double`.

![GcfFunctionCallTest_ecc](GcfFunctionCallTest_ecc.svg)

## Interface

| Name | Type | Explanation |
|---|---|---|
| `REQ` | Event input | Starts the calculation |
| `CNF` | Event output (`With` = `FromGcf`, `FromCall`, `Passed`) | Confirmation |
| `FromGcf` | `INT` | `DOUBLE_VALUE` from the GCF |
| `FromCall` | `INT` | `F_Double(BASE_VALUE)`, computed directly in ST |
| `Passed` | `BOOL` | TRUE when both values are 14 |

## Program Flow

The ECC has two states: `START` and `REQ`. In `REQ` the algorithm `REQ` runs and sends `CNF`:

```
FromGcf := DOUBLE_VALUE;
FromCall := Meins::GcfFunctionCall::F_Double(BASE_VALUE);
Passed := (FromGcf = 14) AND (FromCall = 14);
```

## Summary

The block checks that the value from the GCF and the direct function call give the same result (14).

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
