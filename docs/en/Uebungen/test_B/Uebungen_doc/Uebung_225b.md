# Exercise_225b: Triangle Setpoint Marker with PositionMarkerFS

![Uebung_225b_network](./Uebung_225b_network.svg)

* * * * * * * * * *

## Introduction

This exercise is functionally identical to `Uebung_225`, but replaces the individually wired function blocks `F_ADD_Center`/`F_REAL_TO_INT_Pos`/`Q_ChildPosition_Dreieck` with the new, reusable function block `PositionMarkerFS` (`isobus::UT::Q`). `Uebung_225` itself remains unchanged as a separate reference.


## Function Blocks (FBs) Used

- **Setpoint_N**: Terminal input (Type: `isobus::UT::io::NumericValue::NumericValue_PHYS`)

- **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`

- **Explanation**: Reads changes to the value entered via `InputNumber_Sollwert` (VT object 9000) and returns it as the physical value `REAL` via `rPhys` (range -42…+42).

- **Marker_Triangle**: Position marker (Type: `isobus::UT::Q::PositionMarkerFS`)

- **Parameters**: `stObj = Container_PositionMarker`, `xScale = TRUE`

- **Explanation**: Handles the complete movement of the triangle: internally adds the center offset, brackets the result to the valid movement range (with `xOver`/`xUnder` feedback), converts `REAL` → `INT`, and writes the position to the polygon object using Change Child Position. Child ID, Parent ID, movement range, and center offset are bundled in the constant `Container_PositionMarker` (type `PositionMarker_S`), which is automatically generated from the geometry `.jop`. `xScale = TRUE` activates the multiplication of the pixel offset by the DataMask scaling factor.

- **Actual Value_N**: Terminal output (type: `isobus::UT::Q::Q_NumericValue_PHYS`)

- **Parameter**: `stObj = NumberVariable_Istwert_N`

- **Explanation**: Writes the same physical target value back unchanged as the actual value; updates `InputNumber_Istwert` and the existing bargraph position pointer.


### Sub-modules: none

The module `PositionMarkerFS` itself is not a composite sub-app in this exercise, but rather a pre-built library component (`Ventilsteuerung\4diacIDE-workspace\.lib\isobus-3.0.0\typelib\UT\Q\PositionMarkerFS.fbt`).

## Program Flow and Connections

1. If the operator changes `InputNumber_Sollwert`, `Sollwert_N.IND` fires and returns the physical value via `Sollwert_N.rPhys`.

2. The event `Sollwert_N.IND` triggers `Marker_Dreieck.REQ` and `Istwert_N.REQ` in parallel.

3. `Sollwert_N.rPhys` is directly routed to `Marker_Dreieck.rValue`; `PositionMarkerFS` internally calculates the offset, bracketing, and pixel position and writes them to the triangle object.

4. In parallel, `Sollwert_N.rPhys` is passed unchanged to `Istwert_N.rPhys` and written back there as the actual value.

## Summary

This exercise demonstrates how a recurring task (positioning target value markers, including bracketing and range monitoring) can be encapsulated in a single reusable building block (`PositionMarkerFS`) instead of being reassembled from individual building blocks each time, as in `Uebung_225`. For the version fully wired via AR adapters, see `Uebung_225b_AX` in `test_AX`.


---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
