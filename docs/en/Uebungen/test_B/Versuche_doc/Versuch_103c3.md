# Versuch_103c3: DigitalInput to DigitalOutput via an AX demultiplexer adapter

![Versuch_103c3_network](./Versuch_103c3_network.svg)

* * * * * * * * * *

## Introduction

Counterpart to `Versuch_103c2`: instead of a multiplexer (`AX_AUI_MUX_3`), this uses a 3-way demultiplexer adapter (`AX_AUI_DEMUX_3`), which distributes one input signal to one of several possible outputs.

## Function Blocks Used (FBs)

- **DigitalInput_I1** – Type: `logiBUS::io::DI::logiBUS_IXA`
    - Parameter: `QI = TRUE`, `Input = Input_I1`
- **AX_DEMUX_3** – Type: `adapter::selection::unidirectional::AX_AUI_DEMUX_3`
    - Functionality: 3-way demultiplexer adapter (unidirectional) - distributes one input signal to up to three outputs. Here, only the first output (`OUT1`) is wired.
- **DigitalOutput_Q1** – Type: `logiBUS::io::DQ::logiBUS_QXA`
    - Parameter: `QI = TRUE`, `Output = Output_Q1`

## Program Flow and Connections

Adapter connections: `DigitalInput_I1.IN` → `AX_DEMUX_3.IN`, `AX_DEMUX_3.OUT1` → `DigitalOutput_Q1.OUT`. The physical input `Input_I1` is routed via the demultiplexer adapter to its first output branch and switches the physical output `Output_Q1`.

## Summary

Shows the simplest wiring of an `AX_AUI_DEMUX_3` adapter with only one active output branch - the counterpart to `Versuch_103c2`'s multiplexer wiring.
