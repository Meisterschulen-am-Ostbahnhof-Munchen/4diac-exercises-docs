# Versuch_402: Assembling an array from individual values

![Versuch_402_network](./Versuch_402_network.svg)

* * * * * * * * * *

## Introduction

Unlike the previous experiments (a fixed array constant via `PROVIDE_ARR_0008_INT`), the array here is assembled from eight individual scalar values (`VALUES2ARRAY_8_INT`), triggered by an `INIT` block that repeatedly re-triggers itself.

## Function Blocks Used (FBs)

- **INIT** – Type: `iec61131::booleanOperators::INIT`
    - Functionality: fires `INITO` on startup, which is fed straight back into its own `REQ` input - this kicks off the processing chain.
- **VALUES2ARRAY_8_INT** – Type: `eclipse4diac::convert::VALUES2ARRAY_8_INT`
    - Parameter: `IN_1..IN_8 = 1, 22, 333, 4444, 333, 22, 1, 0`
    - Functionality: assembles an 8-element array from eight individual scalar values.
- **GET_AT_INDEX** – Type: `eclipse4diac::convert::GET_AT_INDEX`, parameter: `INDEX = 0` (element `1`)
- **F_MOVE** – Type: `iec61131::selection::F_MOVE`, attribute: `DataType = INT`
- **CountOfElements** – Type: `eclipse4diac::utils::arrays::F_LEN_ARRAY`
- **F_UPPER_BOUND** – Type: `iec61131::arrays::F_UPPER_BOUND`
- **F_LOWER_BOUND** – Type: `iec61131::arrays::F_LOWER_BOUND`

## Program Flow and Connections

`INIT.CNF` triggers `VALUES2ARRAY_8_INT.REQ`, which assembles the eight individual values into an array. Its `CNF` simultaneously triggers three consumers (`GET_AT_INDEX`, `CountOfElements`, `F_LOWER_BOUND`, `F_UPPER_BOUND`), all of which receive the just-assembled array via `OUT`. `GET_AT_INDEX` reads index 0 and passes the value on via `F_MOVE`.

## Summary

Shows the alternative way of building an array from individual values (`VALUES2ARRAY_8_INT`) instead of from an array constant, plus evaluation of element count and index bounds.
