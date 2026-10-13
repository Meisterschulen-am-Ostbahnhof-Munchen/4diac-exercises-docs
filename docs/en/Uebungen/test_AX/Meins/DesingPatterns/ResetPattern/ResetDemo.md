# ResetDemo: Reset Pattern (Bypassing the Start/Stop Gate)

![ResetDemo_network](./ResetDemo_network.svg)

* * * * * * * * * *

## Introduction

`ResetDemo` is the demo for the **Reset pattern** (IEC 61499 primer course, Module 6, Valeriy Vyatkin, slide 71): `RESET` drives its own `TrueUntil` directly and thereby **bypasses the start/stop gate** (`E_SR` + `E_PERMIT`) that guards the normal `TRIGGER` path. The reset therefore also works while the plant is stopped.

### SubApp interface

| Name | Kind | Type | Description |
|---|---|---|---|
| `START` | Event input | `Event` | Start the plant: opens the gate for the normal `TRIGGER` path |
| `STOP` | Event input | `Event` | Stop the plant: closes the gate |
| `TRIGGER` | Event input | `Event` | Ask the normal step to run - only reaches it while the plant is started |
| `RESET` | Event input | `Event` | Ask the reset step to run - bypasses the gate, works even while stopped |
| `DONE` | Event output | `Event` | The normal step completed |
| `RESET_DONE` | Event output | `Event` | The reset step completed |
| `InPosition` | Data input | `BOOL` | Demo: the normal step's actuator reached its position |
| `AtHome` | Data input | `BOOL` | Demo: the reset step's actuator reached the home position |
| `Running` | Data output | `BOOL` | Current start/stop state (`E_SR.Q`); `RESET` ignores it |

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `StartStopHandle` | [E_SR](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SR/) | – | Start/stop latch: `START` sets (`S`), `STOP` resets (`R`) |
| `Gate` | [E_PERMIT](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_PERMIT/) | – | Gate for the normal path, permit from `StartStopHandle.Q` |
| `Worker` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | – | Normal step |
| `ResetWorker` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | – | Reset step (separate instance) |

## Program Flow and Connections

- **Normal path**: `TRIGGER` → `Gate.EI` → `Gate.EO` → `Worker.TRIGGER`; `Worker.DONE` → `DONE`; `InPosition` → `Worker.inPosition`. The gate is open while `StartStopHandle.Q` = `TRUE` (`StartStopHandle.Q` → `Gate.PERMIT`).
- **Start/stop**: `START` → `StartStopHandle.S`, `STOP` → `StartStopHandle.R`; the state is additionally output as `Running`.
- **Reset path**: `RESET` → `ResetWorker.TRIGGER` (per the comment "bypasses StartStopHandle/Gate entirely - works even while stopped"); `ResetWorker.DONE` → `RESET_DONE`; `AtHome` → `ResetWorker.inPosition`.

## Summary

`ResetDemo` shows how a reset command is routed through its own path, independent of the start/stop gate, so that it works even when the plant is stopped.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
