# Versuch_404: Array sum and single-element access

![Versuch_404_network](./Versuch_404_network.svg)

* * * * * * * * * *

## Introduction

Extends `Versuch_409` with a second, parallel consumer: alongside the sum computation, a single array element is now also read by index.

## Function Blocks Used (FBs)

- **INIT_ARR_8_INT** – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [0, 1, 22, 333, 4444, 333, 22, 1]`
- **GET_AT_INDEX** – Type: `eclipse4diac::convert::GET_AT_INDEX`
    - Parameter: `INDEX = 3`
    - Inputs: `REQ`, `IN_ARRAY`; output: `OUT`
    - Functionality: reads the element at index 3 of the array (here: `333`).
- **SUM** – Type: `logiBUS::utils::dyn_arr::SUM`
    - Functionality: sums all array elements.

## Program Flow and Connections

`INIT_ARR_8_INT.INITO` triggers both consumers simultaneously: `GET_AT_INDEX.REQ` and `SUM.REQ`. Both receive the same array via `D1`. `GET_AT_INDEX` reads index 3 (`333`), `SUM` computes the total sum - both branches run independently of each other in parallel.

## Summary

Shows that multiple consumers can process the same array signal simultaneously (in parallel) without affecting each other.
