# Exercise_225: Triangle Setpoint Marker

![Uebung_225_network](./Uebung_225_network.svg)

* * * * * * * * * *

## Introduction

This exercise positions a small triangle (center marker) within a container on the Virtual Terminal, based on a setpoint entered via an input field (ISO 11783-6 Annex F.16, Change Child Position). Additionally, the same setpoint is written back unchanged as the actual value, so that the input field and bar graph pointer synchronously display the same value.


## Function Blocks (FBs) Used

- **Sollwert_N**: Terminal input (Type: `isobus::UT::io::NumericValue::NumericValue_PHYS`)

- **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`

- **Explanation**: Reads changes to the value entered via `InputNumber_Sollwert` (VT object 9000). The VT reports value changes of an input field bound to a `NumberVariable` under the object ID of the variable (`NumberVariable_Sollwert`, 21000), not under the ID of the input field itself. It returns the physical value (already offset by -42, range -42…+42) as `REAL` over `rPhys`.

- **F_ADD_Center**: Addition (Type: `iec61131::arithmetic::F_ADD`)

- **Parameter**: `IN2 = REAL#42.0`

- **Explanation**: Adds the center offset of 42 to the target value to convert it to the actual pixel X position (0…84) within the container.

- **F_REAL_TO_INT_Pos**: Type conversion (Type: `iec61131::conversion::F_REAL_TO_INT`)

- **Parameters**: None

- **Explanation**: Converts the calculated `REAL` position value to a `INT` pixel value.

- **Q_ChildPosition_Dreieck**: Object positioning (Type: `isobus::UT::Q::Q_ChildPosition`)

- **Parameters**: `u16ObjId = Polygon_Bargraph_Mittelmarker` (child object, triangle), `u16ObjIdParent = Container_Sollwertmarker` (parent object), `xScale = TRUE`, `s16Yposition = 0`

- **Explanation**: Writes the new X-position of the triangle within the container using the Change-Child-Position command. `xScale = TRUE` ensures that the pixel offset is additionally multiplied by the DataMask scaling factor. Y remains constant at 0.

- **Istwert_N**: Terminal output (Type: `isobus::UT::Q::Q_NumericValue_PHYS`)

- **Parameter**: `stObj = NumberVariable_Istwert_N`

- **Explanation**: Writes the same physical setpoint back unchanged as the actual value; this updates both `InputNumber_Istwert` and the existing bargraph position pointer.

### Sub-modules: none

## Program Flow and Connections

1. If the operator changes `InputNumber_Sollwert`, `Sollwert_N.IND` fires and outputs the physical value via `Sollwert_N.rPhys`.


2. The event `Sollwert_N.IND` triggers two parallel chains: firstly, `F_ADD_Center.REQ` (position calculation), and secondly, directly, `Istwert_N.REQ` (actual value write-back).

3. `Sollwert_N.rPhys` is routed to `F_ADD_Center.IN1`; `F_ADD_Center` adds `REAL#42.0` and passes the result to `OUT` as soon as `F_ADD_Center.CNF` triggers `F_REAL_TO_INT_Pos.REQ`.

4. `F_REAL_TO_INT_Pos` converts the value of `REAL` to `INT`; its event `CNF` triggers `Q_ChildPosition_Dreieck.REQ`, while the data value is updated via `OUT` to `s16Xposition`.

5. `Q_ChildPosition_Dreieck` writes the new triangle position to the VT.

6. In parallel, `Sollwert_N.rPhys` is updated unchanged to `Istwert_N.rPhys` and written back as the actual value at `Istwert_N.REQ`.

## Summary

This exercise demonstrates the classic, step-by-step wired implementation of virtual terminal object positioning: read the target value, convert it to pixel coordinates (offset addition + type conversion), write the position, and simultaneously return the actual value. For the fully adapter-based version, see `Uebung_225_AX` in `test_AX`, which implements the same function using a pure AR/AI adapter chain instead of individual event/data connections.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
