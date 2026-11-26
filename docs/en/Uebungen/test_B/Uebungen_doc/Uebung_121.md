# Exercise_121: ISOBUS Name Exercise

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/a6872e59-1dfc-4132-a118-aff1bc7bc944)
This article describes the logiBUS® exercise `Uebung_121`

----

## Overview

This exercise demonstrates the counterpart to 120: Reading the controller's own identity.

The controller returns its own 64-bit name via the parameter `member = thisMember` on the function block `NmGetCfInfo`. The function block `NmSetName` allows the individual fields of the controller's own identity (e.g., the function instance) to be dynamically adjusted at runtime before being communicated over the bus.

![Uebung_121_network](./Uebung_121_network.svg)

## Pattern with `NmGetCfInfo` (IND/RSP)

`NmGetCfInfo` reports a network event with `IND` and then waits for `RSP` (`bwaitingForRSP = TRUE`). Only after `RSP` does the block deliver the next event from its internal queue. This exercise is therefore built as follows:

- `NmGetCfInfo.sNetEv` goes to `STRUCT_DEMUX`, whose `CNF` goes to `NmGetCfInfo.RSP` (display only, the connection is hidden in the IDE).

State of the conversion: 2026-11-17, not yet tested on hardware.
