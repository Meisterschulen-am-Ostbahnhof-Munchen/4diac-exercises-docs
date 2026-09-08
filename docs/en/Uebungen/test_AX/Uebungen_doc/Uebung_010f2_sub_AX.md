# Exercise_010f2_sub_AX: Sub-Block for AuxFunction2_X1 with GreenWhiteBackground (Object ID only once)

![Uebung_010f2_sub_AX_network](./Uebung_010f2_sub_AX_network.svg)

* * * * * * * * * *

## Introduction

This SubApp type encapsulates the complete wiring from `Uebung_010f_AX` (Aux button → output + background color) in a reusable sub-block. Only the two actually required parameters, `u16ObjIdA` and `Output`, are exposed externally – the object ID only needs to be specified once, no longer separately for the Aux input and the background color block.


## Function Blocks (FBs) Used

- **AuxFunction2_X1**: Auxiliary input (Type: `isobus::UT::io::Auxiliary::IN::Aux_IXA`)

- **Parameters**: `QI = TRUE` (`u16ObjId` is received externally via the interface)

- **Explanation**: Returns the Boolean state of the auxiliary button/joystick assigned via `u16ObjIdA` as an AX adapter signal (`IN`).

- **AX_SPLIT_2**: Signal distributor (Type: `adapter::events::unidirectional::AX_SPLIT_2`)

- **Parameters**: None

- **Explanation**: Distributes the single incoming AX signal to two outputs (`OUT1`, `OUT2`).

- **DigitalOutput_Q1**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: `QI = TRUE` (`Output` is received externally via the interface)

- **Explanation**: Physical output that is directly switched by the Aux signal (`AX_SPLIT_2.OUT1`).


### Sub-Blocks: GreenWhiteBackground2_AX

- **GreenWhiteBackground2_AX**: Background color block for Auxiliary Function Type 2 objects (Type: `MyLib::sys::GreenWhiteBackground2_AX`)

- **Parameters**: `u16ObjIdA` is received externally via the interface

- **Explanation**: Sets the background color to green upon a signal at `DI1`, otherwise white – simultaneously on the VT screen (`Q_BackgroundColour`) and on the Aux Handle itself (`Q_BackgroundColourAux`), as already described in `Uebung_010f_AX`. This wiring remains unchanged compared to `Uebung_010f_AX`; it is not a necessary simplification, but rather intentional.

## Interface (SubApp Interface)

- **u16ObjIdA** (`UINT`, Default `ID_NULL`): the only externally configurable object ID of the auxiliary control.

- **Output** (`logiBUS::io::DQ::logiBUS_DO_S`, Default `logiBUS_DO::Invalid`): the only externally configurable assignment of the physical digital output.

## Program Flow and Connections

1. **Distribution of the Object ID**: A `DataConnection` internally splits the single input `u16ObjIdA` into two destinations: `AuxFunction2_X1.u16ObjId` and `GreenWhiteBackground2_AX.u16ObjIdA`. The user of this sub-module only needs to specify the object ID once when calling it.

2. **Output Distribution**: Input `Output` is forwarded to `DataConnection` and then to `DigitalOutput_Q1.Output`.

3. **Signal Acquisition**: When the assigned aux button/joystick is pressed, `AuxFunction2_X1` sends an AX signal to `IN`.

4. **AX Signal Distribution**: `AuxFunction2_X1.IN → AX_SPLIT_2.IN` routes the signal to the distributor, which duplicates it unchanged to `OUT1` and `OUT2`.

5. **Physical Output**: `AX_SPLIT_2.OUT1 → DigitalOutput_Q1.OUT` switches the assigned physical output synchronously with the Aux button.

6. **Visual Feedback**: `AX_SPLIT_2.OUT2 → GreenWhiteBackground2_AX.DI1` simultaneously sets the background color on the VT screen and the Aux handle.


## Summary

`Uebung_010f2_sub_AX` is the reusable sub-module behind `Uebung_010f2_AX`: internally, it uses exactly the same wiring as `Uebung_010f_AX` (auxiliary input → `AX_SPLIT_2` → physical output + `GreenWhiteBackground2_AX`), but reduces the externally visible interface to two parameters (`u16ObjIdA`, `Output`) and distributes the object ID internally via a data connection to both locations that require it. This means the object ID only needs to be specified once when using this module.


---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
