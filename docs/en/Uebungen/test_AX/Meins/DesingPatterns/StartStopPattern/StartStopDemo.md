# StartStopDemo: Start/Stop pattern

![StartStopDemo_network](./StartStopDemo_network.svg)

* * * * * * * * * *

## Introduction

`StartStopDemo` shows the Start/Stop pattern: an `E_SR` holds the persistent start/stop state and, via `E_PERMIT`, enables or blocks forwarding of the event to `TrueUntil.TRIGGER` (type comment).

Interface: event inputs `START`, `STOP` and `TRIGGER`; event output `DONE`; input `InPosition` (BOOL, simulates the actuator feedback); output `Running` (BOOL, `TRUE` while started).

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `StartStopHandle` | [`iec61499::events::E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_SR/) | - | Set/reset latch: `START` → `S`, `STOP` → `R`; output `Q` is the start/stop state. |
| `Gate` | [`iec61499::events::E_PERMIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_PERMIT/) | - | Forwards the event `EI` as `EO` only while `PERMIT` = `TRUE`. |
| `Worker` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | - | Step block from the Chain of Actions pattern. |

## Program Flow and Connections

**Event connections:**

1. `START` → `StartStopHandle.S`; `STOP` → `StartStopHandle.R`.
2. `TRIGGER` → `Gate.EI`; `Gate.EO` → `Worker.TRIGGER`.
3. `Worker.DONE` → `DONE`.

**Data connections:**

1. `StartStopHandle.Q` → `Gate.PERMIT`.
2. `StartStopHandle.Q` → `Running`.
3. `InPosition` → `Worker.inPosition`.

A `TRIGGER` reaches `Worker` only if the plant was started beforehand (according to the interface comment, `DONE` fires only then). `TrueUntil` is a basic FB in the project folder `DesingPatterns/ChainOfActionsPattern`.

## Summary

The demo separates the persistent start/stop state (E_SR) from the permission for individual jobs (E_PERMIT) and leaves the worker block unchanged.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
