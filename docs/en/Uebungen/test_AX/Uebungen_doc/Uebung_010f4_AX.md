# Exercise_010f4_AX: BAD STYLE (Point Deduction!) — SoftKey_F1 OR AuxFunction2_X1 on DigitalOutput_Q1 with 2x GreenWhiteBackground

![Uebung_010f4_AX_network](./Uebung_010f4_AX_network.svg)

* * * * * * * * * *

## Introduction

**This file is intentionally left as a negative example** (explicitly marked in the source code as "BAD STYLE - Point Deduction!"). Functionally, it achieves the same result as `Uebung_010f3_AX` (SoftKey OR Aux button switched together `Q1`), but does so with unnecessarily bloated wiring: instead of the intended function block `GreenWhiteBackground3_AX`, two separate instances, `GreenWhiteBackground1_AX` and `GreenWhiteBackground2_AX`, are used. This page intentionally documents the error, not a recommended solution – for the correct implementation, see `Uebung_010f3_AX`.

## Function Blocks (FBs) Used

- **SoftKey_F1**: VT SoftKey input (Type: `isobus::UT::io::Softkey::Softkey_IXA`)

- **Parameters**: `QI = TRUE`, `u16ObjId = SoftKey_F1`

- **Explanation**: Returns the Boolean state of the SoftKey as an AX adapter output `IN`.


- **AuxFunction2_X1**: Auxiliary input (Type: `isobus::UT::io::Auxiliary::IN::Aux_IXA`)

- **Parameters**: `QI = TRUE`, `u16ObjId = AuxFunction2_X1`

- **Explanation**: Returns the Boolean state of the assigned physical aux button/joystick as the AX adapter output `IN`.

- **AX_OR_2**: Adapter OR gate (Type: `adapter::booleanOperators::AX_OR_2`)

- **Parameters**: None

- **Explanation**: Combines the two AX signals from the soft key and the aux button using an OR gate to create a single signal.

- **AX_SPLIT_3**: Adapter signal distributor to three destinations (Type: `adapter::events::unidirectional::AX_SPLIT_3`)

- **Parameters**: None

- **Explanation**: Distributes the OR-connected signal to **three** destinations instead of two because two separate background color blocks need to be controlled here instead of a combined one – the actual design flaw of this exercise.

- **DigitalOutput_Q1**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QXA`)

- **Parameters**: `QI = TRUE`, `Output = Output_Q1`

- **Explanation**: Switches the physical output `Q1` as soon as the SoftKey or Aux button is active.


### Sub-Blocks: GreenWhiteBackground1_AX, GreenWhiteBackground2_AX

- **GreenWhiteBackground1_AX** (Type: `MyLib::sys::GreenWhiteBackground1_AX`)

- **Parameters**: `u16ObjId = SoftKey_F1`

- **Explanation**: Sets the background color of the SoftKey object on the VT screen. It is required here as a **separate** instance because a combined block was not used.

- **GreenWhiteBackground2_AX** (Type: `MyLib::sys::GreenWhiteBackground2_AX`)

- **Parameters**: `u16ObjIdA = AuxFunction2_X1`

- **Explanation**: Sets the background color of the Aux object on the VT screen and Aux handle. Also a **separate** instance instead of being combined in `GreenWhiteBackground3_AX`.


## Program Flow and Connections

1. `SoftKey_F1.IN` → `AX_OR_2.IN1` and `AuxFunction2_X1.IN` → `AX_OR_2.IN2`: Both controls each supply an AX signal to the OR gate.

2. `AX_OR_2.OUT` → `AX_SPLIT_3.IN`: The combined signal is distributed to **three** instead of two devices.

3. `AX_SPLIT_3.OUT1` → `DigitalOutput_Q1.OUT`: The physical output `Q1` switches ON as soon as the SoftKey OR Aux button is active.

4. `AX_SPLIT_3.OUT2` → `GreenWhiteBackground1_AX.DI1`: Separate control of the soft key background color.

5. `AX_SPLIT_3.OUT3` → `GreenWhiteBackground2_AX.DI1`: Separate control of the auxiliary background color.

**What went wrong here:** By using two separate `GreenWhiteBackground1_AX`/`GreenWhiteBackground2_AX` instances instead of the combined module `GreenWhiteBackground3_AX`, a `AX_SPLIT_3` (3 targets) was needed instead of a simple `AX_SPLIT_2` (2 targets). The color selection logic (internally `AX_SEL` White/Green) is therefore executed twice instead of once – resulting in unnecessary resource consumption and unnecessarily large diagrams, without any functional advantage over `Uebung_010f3_AX`.

## Summary

This exercise is intentionally not a model, but rather a cautionary example: Before individually assembling several generic building blocks for a combined case (normal VT object + Aux object), you should first check whether a suitable, ready-made building block already exists in the library (here: `GreenWhiteBackground3_AX`, see `Uebung_010f3_AX`). Comparing both sub-apps in the 4diac editor immediately reveals the difference in resource consumption.


## Summary ---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
