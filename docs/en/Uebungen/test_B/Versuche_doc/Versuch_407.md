# Versuch_407: Index access and forwarding to a second array provider

![Versuch_407_network](./Versuch_407_network.svg)

* * * * * * * * * *

## Introduction

Shows two independent uses of the same array signal: a read-only index access, and forwarding the complete array to a second `PROVIDE_ARR_0008_INT` instance, which here is used not as a constant source but as an externally-driven receiver.

## Function Blocks Used (FBs)

- **INIT_ARR_8_INT** – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [0, 1, 22, 333, 4444, 333, 22, 1]`
    - Functionality: array source, fires `INITO` on startup.
- **GET_AT_INDEX** – Type: `eclipse4diac::convert::GET_AT_INDEX`
    - Parameter: `INDEX = 0` (element `0`)
- **INIT_ARR_8_INT_1** – Type: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT` (second instance, with no `D1` parameter of its own)
    - Functionality: here - unlike the usual role in this experiment series - receives its array and event from outside (`D1`/`INIT` act as inputs fed by `INIT_ARR_8_INT`), instead of serving as a fixed constant source itself.

## Program Flow and Connections

`INIT_ARR_8_INT.INITO` triggers two targets simultaneously: `GET_AT_INDEX.REQ` (reads index 0) and `INIT_ARR_8_INT_1.INIT` (forwards the complete array to the second provider instance). Both data paths are fed from `INIT_ARR_8_INT.D1`.

## Summary

An unusual but instructive variant: the same `PROVIDE_ARR_...` block type can appear both as a pure constant source (`INIT_ARR_8_INT`) and as an externally-driven receiver (`INIT_ARR_8_INT_1`).
