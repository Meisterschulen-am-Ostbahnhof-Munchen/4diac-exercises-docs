# Versuch_405: Array copy with zero values (constant variant of Versuch_403)

![Versuch_405_network](./Versuch_405_network.svg)

* * * * * * * * * *

## Introduction

Structurally identical to `Versuch_403` (array copy via `ARRAY2ARRAY_8_INT`, index access, element count, bounds), but with a fixed array constant (`PROVIDE_ARR_0008_INT`) instead of the variant assembled from individual values - triggered directly from its own `INITO`, without the additional `INIT` block used in `Versuch_402`/`Versuch_403`.

**Note:** The block here carries the instance name `VALUES2ARRAY_8_INT`, but is actually of type `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT` - presumably a leftover instance name from `Versuch_402`/`Versuch_403` that was not updated when the block type was changed. The description below follows the actual type.

## Function Blocks Used (FBs)

- **VALUES2ARRAY_8_INT** (instance name) – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [0, 0, 0, 0, 0, 0, 0, 0]`
- **GET_AT_INDEX** – Type: `eclipse4diac::convert::GET_AT_INDEX`, parameter: `INDEX = 0`
- **F_MOVE** – Type: `iec61131::selection::F_MOVE`, attribute: `DataType = INT`
- **CountOfElements** – Type: `eclipse4diac::utils::arrays::F_LEN_ARRAY`
- **F_UPPER_BOUND** / **F_LOWER_BOUND** – Type: `iec61131::arrays::F_UPPER_BOUND`/`F_LOWER_BOUND`
- **ARRAY2ARRAY_8_INT** – Type: `eclipse4diac::convert::ARRAY2ARRAY_8_INT` – copies the array into an independent second instance.
- **CountOfElements_1** – Type: `eclipse4diac::utils::arrays::F_LEN_ARRAY` – length of the copy.

## Program Flow and Connections

`INITO` directly triggers all five consumers simultaneously (`GET_AT_INDEX`, `CountOfElements`, `F_LOWER_BOUND`, `F_UPPER_BOUND`, `ARRAY2ARRAY_8_INT`); `ARRAY2ARRAY_8_INT.CNF` in turn triggers `CountOfElements_1.REQ` (length of the copy).

## Summary

Same structure as `Versuch_403`, but with a fixed array constant instead of one assembled from individual values - and without the additional `INIT` self-trigger.
