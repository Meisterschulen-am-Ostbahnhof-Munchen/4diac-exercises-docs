# Versuch_406: Array statistics (sum, min, max, average, length)

![Versuch_406_network](./Versuch_406_network.svg)

* * * * * * * * * *

## Introduction

Extends the parallel evaluation of an array (see `Versuch_404`) into a full statistics suite: sum, minimum, maximum, average, element count, and a targeted index access are all computed simultaneously from the same array.

## Function Blocks Used (FBs)

- **INIT_ARR_8_INT** – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [0, 1, 22, 333, 4444, 333, 22, 1]`
- **GET_AT_INDEX** – Type: `eclipse4diac::convert::GET_AT_INDEX`
    - Parameter: `INDEX = 4` (element `4444`)
- **F_MOVE** – Type: `iec61131::selection::F_MOVE`
    - Attribute: `DataType = INT`
    - Functionality: takes over the value read by `GET_AT_INDEX` (pure pass-through/type conversion).
- **ARR_MIN** – Type: `logiBUS::utils::dyn_arr::ARR_MIN` – determines the smallest element.
- **ARR_MAX** – Type: `logiBUS::utils::dyn_arr::ARR_MAX` – determines the largest element.
- **AVG** – Type: `logiBUS::utils::dyn_arr::AVG` – computes the average.
- **SUM** – Type: `logiBUS::utils::dyn_arr::SUM` – computes the sum.
- **CountOfElements** – Type: `eclipse4diac::utils::arrays::F_LEN_ARRAY` – determines the element count.

## Program Flow and Connections

`INIT_ARR_8_INT.INITO` triggers all six consumers (`SUM`, `CountOfElements`, `ARR_MAX`, `ARR_MIN`, `AVG`, `GET_AT_INDEX`) simultaneously - each receives the same array via `D1`. `GET_AT_INDEX` reads index 4 (`4444`) and passes the value on via `F_MOVE`. All six evaluations run independently and in parallel.

## Summary

Shows the full, parallel standard statistics over an array (sum/min/max/average/length) plus a targeted index access - the precursor to `Versuch_401`, which additionally includes the array bounds (`F_UPPER_BOUND`/`F_LOWER_BOUND`).
