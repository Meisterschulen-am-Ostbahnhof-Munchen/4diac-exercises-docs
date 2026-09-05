# Versuch_409: Array sum

![Versuch_409_network](./Versuch_409_network.svg)

* * * * * * * * * *

## Introduction

First extension of `Versuch_400`: the provided 8-element array is now actually processed - via summation using `SUM`.

## Function Blocks Used (FBs)

- **INIT_ARR_8_INT** – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [44, 0, 5, 0, 7, 8, 0, 0]`
    - Functionality: provides the fixed 8-element array and fires `INITO` on startup.
- **SUM** – Type: `logiBUS::utils::dyn_arr::SUM`
    - Inputs: `REQ`, `A` (array)
    - Functionality: sums all elements of the given array.

## Program Flow and Connections

1. **Initialization**: `INIT_ARR_8_INT` fires `INITO` on startup and provides its array at output `D1`.
2. **Summation**: `INITO` triggers `SUM.REQ`, `SUM` receives the array via `D1` → `A` and computes the sum of all eight elements (44+0+5+0+7+8+0+0 = 64).

## Summary

Shows the simplest form of array aggregation: one array provider, one consumer (`SUM`). Builds directly on `Versuch_400`.
