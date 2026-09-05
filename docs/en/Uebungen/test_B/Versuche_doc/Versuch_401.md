# Versuch_401: Array statistics with bounds

![Versuch_401_network](./Versuch_401_network.svg)

* * * * * * * * * *

## Introduction

The most complete stage of the array statistics experiment series: extends `Versuch_406` with the array's index bounds (`F_UPPER_BOUND`/`F_LOWER_BOUND`) - eight parallel evaluations of the same array in total.

## Function Blocks Used (FBs)

- **INIT_ARR_8_INT** – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [44, 0, 5, 0, 7, 8, 0, 0]`
- **GET_AT_INDEX** – Type: `eclipse4diac::convert::GET_AT_INDEX`
    - Parameter: `INDEX = 0` (element `44`)
- **F_MOVE** – Type: `iec61131::selection::F_MOVE`, attribute: `DataType = INT`
- **ARR_MIN** / **ARR_MAX** / **AVG** / **SUM** – Type: `logiBUS::utils::dyn_arr::*` (see `Versuch_406`)
- **CountOfElements** – Type: `eclipse4diac::utils::arrays::F_LEN_ARRAY`
- **F_UPPER_BOUND** – Type: `iec61131::arrays::F_UPPER_BOUND` – upper index bound of the array (here: 7).
- **F_LOWER_BOUND** – Type: `iec61131::arrays::F_LOWER_BOUND` – lower index bound of the array (here: 0).

## Program Flow and Connections

`INIT_ARR_8_INT.INITO` triggers all eight consumers (`SUM`, `GET_AT_INDEX`, `CountOfElements`, `ARR_MAX`, `AVG`, `ARR_MIN`, `F_UPPER_BOUND`, `F_LOWER_BOUND`) simultaneously, each receiving the same array via `D1`. `GET_AT_INDEX` reads index 0 (`44`) and passes the value on via `F_MOVE`.

## Summary

The most comprehensive statistics variant in this experiment series: sum, min, max, average, element count, index bounds, and a targeted index access - all eight evaluations in parallel from a single array source.
