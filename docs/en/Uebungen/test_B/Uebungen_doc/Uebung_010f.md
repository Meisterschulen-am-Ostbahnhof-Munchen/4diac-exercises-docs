# Exercise_010f: AuxFunction2_X1 on DigitalOutput_Q1 with GreenWhiteBackground

![Uebung_010f_network](./Uebung_010f_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the classic (not wired via AX adapter) version of `Uebung_010f_AX`, building upon `Uebung_010b1`: In addition to controlling `DigitalOutput_Q1`, the status of `AuxFunction2_X1` is reported back as a background color (green = ON, white = OFF).


## Function Blocks (FBs) Used

- **AuxFunction2_X1**: Auxiliary Function Input (Type: `isobus::UT::io::Auxiliary::IN::Aux_IX`)
    - **Parameters**: `QI = TRUE`, `u16ObjId = AuxFunction2_X1`
    - **Explanation**: Returns the Boolean state of the assigned physical auxiliary button/joystick via the event/data interface `IND`/`IN`.
- **DigitalOutput_Q1**: logiBUS digital output (Type: `logiBUS::io::DQ::logiBUS_QX`)
    - **Parameters**: `QI = TRUE`, `Output = Output_Q1` (stored as invisible parameters in the network)
    - **Description**: Switches the physical output Q1 according to the Aux state.

### Sub-Blocks: GreenWhiteBackground

- **GreenWhiteBackground** (Type: `MyLib::sys::GreenWhiteBackground2`)
    - **Parameters**: `u16ObjIdA = AuxFunction2_X1`
    - **Description**: Sets the background color of the Aux object depending on the Boolean input `DI1` (green = ON, white = OFF). Since `AuxFunction2_X1` is an Auxiliary Function Type 2 object, the assignment must be displayed both on the VT screen and on the aux handle (joystick) itself. These two displays are connected via separate links and are therefore set internally using two commands (`Q_BackgroundColour` for the VT, `Q_BackgroundColourAux` for the aux handle). Therefore, `GreenWhiteBackground2` (not `GreenWhiteBackground1`) must be used here.


## Program Flow and Connections

1. `AuxFunction2_X1.IND` (event triggered by a change in the state of the auxiliary button) is directly connected to `DigitalOutput_Q1.REQ` and `GreenWhiteBackground.REQ` via two parallel EventConnections.

2. The corresponding data value `AuxFunction2_X1.IN` is also routed in parallel to `DigitalOutput_Q1.OUT` and `GreenWhiteBackground.DI1`.

3. Since classic Event/DataConnections—unlike AX adapter plugs—allow multiple destinations from a single source, no explicit split block is required (unlike the AX adapter variant `Uebung_010f_AX`, which requires `AX_SPLIT_2` for this purpose).

4. `GreenWhiteBackground` then colors both the VT object and the Aux handle green or white, depending on the current state.

## Summary

This exercise demonstrates how a physical Aux button can simultaneously switch a digital output and be indicated by a corresponding background color on the VT screen and Aux handle. Unlike the adapter variant `Uebung_010f_AX`, the classic wiring configuration does not require a split block, as standard Event/Data Connections allow for any number of destinations. It remains important to choose `GreenWhiteBackground2` instead of `GreenWhiteBackground1`, as Auxiliary Function Type 2 objects require two separate color feedback signals.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
