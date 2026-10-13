# DecoratorDemo: Decorator Pattern (Permit Gate)

![DecoratorDemo_network](./DecoratorDemo_network.svg)

* * * * * * * * * *

## Introduction

`DecoratorDemo` is the demo for the **Decorator pattern** (IEC 61499 primer course, Module 6, Valeriy Vyatkin, slide 68). According to the source, an `E_PERMIT` gates the event `TRIGGER` in front of the block `TrueUntil` **without modifying `TrueUntil` itself**: the step only runs while the condition `TE` is `TRUE`.

### SubApp interface

| Name | Kind | Type | Description |
|---|---|---|---|
| `TRIGGER` | Event input | `Event` | Ask the step to run - only runs while `TE` = `TRUE` |
| `DONE` | Event output | `Event` | The step completed (only fires if `TE` was `TRUE` when `TRIGGER` arrived) |
| `TE` | Data input | `BOOL` (initial `TRUE`) | Decorator condition |
| `InPosition` | Data input | `BOOL` | Demo: simulates the step's actuator reaching its position |

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `Gate` | [E_PERMIT](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_PERMIT/) | – | Passes `EI` to the output `EO` only if `PERMIT` = `TRUE` |
| `Worker` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | – | The "decorated" step: moves to a position and waits until `inPosition` becomes `TRUE`, then reports `DONE` (see the Chain-of-Actions demo) |

## Program Flow and Connections

1. `TRIGGER` → `Gate.EI`; `TE` → `Gate.PERMIT`.
2. `Gate.EO` → `Worker.TRIGGER`: the event only reaches the step when `TE` = `TRUE`.
3. `InPosition` → `Worker.inPosition`.
4. `Worker.DONE` → `DONE`.

With `TE` = `FALSE` the step is left untouched: `TRIGGER` is discarded at `Gate` and no `DONE` is produced.

## Summary

`DecoratorDemo` shows the Decorator pattern in its simplest form: an upstream `E_PERMIT` adds a permit condition without changing the decorated block `TrueUntil`.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
