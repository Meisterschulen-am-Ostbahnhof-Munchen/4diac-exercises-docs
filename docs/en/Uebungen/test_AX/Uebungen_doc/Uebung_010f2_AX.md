# Exercise_010f2_AX: AuxFunction2_X1 on DigitalOutput_Q1 with GreenWhiteBackground and Subapp

![Uebung_010f2_AX_network](./Uebung_010f2_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise resolves the drawback mentioned in `Uebung_010f_AX`, namely that the object ID `AuxFunction2_X1` had to be entered separately in two places (Aux-FB and `GreenWhiteBackground2_AX`) – just as `Uebung_010c2_AX` does for `Uebung_010c_AX`. The entire network structure now consists of only a single subapp instance at the top level.

## Function Blocks (FBs) Used

At the top level of the subapp, there is no single FB, but rather only the sub-application described below.


### Sub-modules: Exercise_010f2_sub_AX

- **Exercise_010f2_sub_AX** (Type: `Uebungen::Uebung_010f2_sub_AX`)

- **Parameters**: `u16ObjIdA = AuxFunction2_X1` (Object ID of the Aux object), `Output = logiBUS_DO::Output_Q1` (Identity of the physical output)

- **Explanation**: Encapsulates the complete logic from `Uebung_010f_AX` in a reusable sub-application with exactly two inputs: `u16ObjIdA` and `Output`. Internally, it contains the same four building blocks as `Uebung_010f_AX` – `AuxFunction2_X1` (`Aux_IXA`), `AX_SPLIT_2`, `DigitalOutput_Q1` (`logiBUS_QXA`) and `GreenWhiteBackground2_AX` – with identical wiring (`AuxFunction2_X1.IN → AX_SPLIT_2.IN → OUT1 → DigitalOutput_Q1.OUT` and `OUT2 → GreenWhiteBackground2_AX.DI1`). The only difference: An internal (invisible) `DataConnection` distributes the subapp input `u16ObjIdA` simultaneously to `AuxFunction2_X1.u16ObjId` and `GreenWhiteBackground2_AX.u16ObjIdA`, and another to `DigitalOutput_Q1.Output` – meaning the object ID only needs to be specified once when calling the subapp.

## Program Flow and Connections

1. When instantiating the subapp, `Uebung_010f2_sub_AX` is provided with the two parameters `u16ObjIdA` (object ID of the aux object) and `Output` (destination output).


2. Within the sub-application, one DataConnection internally distributes `u16ObjIdA` to `AuxFunction2_X1.u16ObjId` and `GreenWhiteBackground2_AX.u16ObjIdA`; another DataConnection distributes `Output` to `DigitalOutput_Q1.Output`.

3. The actual signal flow is then identical to `Uebung_010f_AX`: `AuxFunction2_X1.IN → AX_SPLIT_2.IN`, `AX_SPLIT_2.OUT1 → DigitalOutput_Q1.OUT` (physical output follows the aux state), and `AX_SPLIT_2.OUT2 → GreenWhiteBackground2_AX.DI1` (background color on the VT screen and aux handle).

## Summary

`Uebung_010f2_AX` demonstrates how a frequently used object ID can be reduced to a single entry point by encapsulating it in its own subapp. Functionally, the exercise is identical to `Uebung_010f_AX`; the only difference lies in the reusability and maintainability of the wiring.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de ](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
