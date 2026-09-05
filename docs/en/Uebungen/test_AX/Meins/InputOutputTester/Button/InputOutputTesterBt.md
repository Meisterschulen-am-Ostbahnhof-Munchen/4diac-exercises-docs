# InputOutputTesterBt: DIDO Tester (VT, no OPC-UA)

![InputOutputTesterBt_network](./InputOutputTesterBt_network.svg)

* * * * * * * * * *

## Introduction

`InputOutputTesterBt` is the original, purely VT-based version of the DIDO tester for **8 digital inputs and 12 digital outputs** — historically the first version of this training example, before OPC-UA connectivity was added. The later [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) reuses the exact same 8+12 structure, but replaces `logiBUS_IXA_BG`/`Button_IXA_TO_logiBUS_QXA_BG` with the `_OPC` variants and adds a `SystemTickSender`.

Like its OPC-UA successor, the exercise is a pure top-level composite with no connections of its own - all logic lives in the reusable sub-blocks.

## Function Blocks (FBs) Used

| SubApp instance | Type | Purpose |
|---|---|---|
| `Input_I1` … `Input_I8` | `MyLib::sys::logiBUS_IXA_BG` | Digital input with VT status display (green/white), **no** OPC-UA publish |
| `Output_Q1` … `Output_Q12` | `MyLib::sys::Button_IXA_TO_logiBUS_QXA_BG` | Digital output, switchable via VT button, **no** OPC-UA subscribe |

### Sub-block: `logiBUS_IXA_BG` (inputs)

- **Type**: SubAppType (`MyLib::sys`)
- **Functionality**: Reads a physical digital input (`logiBUS_IXA`) and displays its state directly via `GreenWhiteBackground1_AX` as a VT background color (green/white). No adapter split, no OPC-UA branch - the simplest stage of this input family.

### Sub-block: `Button_IXA_TO_logiBUS_QXA_BG` (outputs)

- **Type**: SubAppType (`MyLib::sys`)
- **Functionality**: A VT button (`Button_IXA`) switches the physical output (`logiBUS_QXA`) and its VT status color (`GreenWhiteBackground1_AX`) via an `AX_SR` flip-flop. Only one switching source (VT), no OPC-UA subscribe, and therefore none of the feedback-loop decoupling needed by the OPC-UA variant.

## Program Flow and Connections

The exercise itself contains **no connections** (`SubAppNetwork` consists only of SubApp instances with parameters):

1. **8 inputs**: `Input_I1`…`Input_I8` read `Input_I1`…`Input_I8` and mirror them via VT status color.
2. **12 outputs**: `Output_Q1`…`Output_Q12` each connect one physical output to a VT button and VT status color.

**Registration in the training system**: As with all exercises in this system, no dedicated `Application` element is needed - selected via "Change Type" in the 4diac IDE on the system's single `Control` slot.

## Learning Objectives

- Basic pattern for digital inputs/outputs purely via the VT, as a starting point before the OPC-UA extension.
- Direct comparison with [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) shows exactly what a bidirectional web connection additionally requires (adapter splits, `SystemTickSender`, feedback-loop decoupling).

**Difficulty**: Beginner
**Prerequisites**: Basics of the logiBUS digital I/O blocks (`logiBUS_IXA`, `logiBUS_QXA`).

## Summary

`InputOutputTesterBt` is the pure VT baseline version of the DIDO tester: 8 inputs, 12 outputs, no OPC-UA connectivity. Serves as a didactic starting point and comparison baseline for the later OPC-UA extension.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
