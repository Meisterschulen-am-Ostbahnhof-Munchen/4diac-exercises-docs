# Exercise_228_AX: Window Color Feedback on the Triangle (Change Fill Attributes, ISO 11783-6 F.32)

![Uebung_228_AX_network](./Uebung_228_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is a derivative of `Uebung_227_AX.md`: the target value continues to move a triangle (position marker) and a split-bar graph and is written back as the actual value, all via AR adapters. Additionally, the triangle itself turns green as long as the target value is within the window -2…+2, and red as soon as it leaves this window. This exercise demonstrates the use of Change Fill Attributes (ISO 11783-6, F.32) as a simple status indicator on the VT terminal.


## Function Blocks (FBs) Used

- **Sollwert_N**: Terminal input for the setpoint

- **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`

- **Explanation**: Reads the setpoint entered by the operator from the VT terminal and provides it as a physical value (`rPhys`, AR adapter).

- **Split**: Distributor for the setpoint

- **Type**: `adapter::events::unidirectional::AR_SPLIT_4`

- **Parameters**: None

- **Explanation**: Distributes the single incoming AR setpoint to four independent consumers (one more output than in `Uebung_227_AX.md`, which only required three): Triangle position, split bar graph, actual value write-back, and the new color logic.

- **Marker_Dreieck**: Position marker (triangle) on the terminal

- **Type**: `isobus::UT::Q::PositionMarkerFSA`

- **Parameters**: `stObj = Container_PositionMarker`, `xScale = TRUE`

- **Explanation**: Positions the triangle object according to the target value.

- **SplitBar**: Split bar graph on the terminal

- **Type**: `isobus::UT::Q::BargraphSplitFS_AR`

- **Parameter**: `stObj = Bargraph_Split_BargraphSplit`

- **Explanation**: Displays the target value as a split bar graph.

- **Istwert_N**: Terminal output of the actual value

- **Type**: `isobus::UT::Q::Q_NumericValue_PHYSA`

- **Parameter**: `stObj = NumberVariable_Istwert_N`

- **Explanation**: Writes the (passed through unchanged) target value back to the terminal as the actual value.

- **MarkerColor**: Window color logic for the triangle

- **Type**: `isobus::UT::Q::FillWindowFS_AR` (SubApp instance)

- **Parameters**: `u16ObjId = FillStyle_Bargraph_Mittelmarker_Gruen`, `rWindowMin = REAL#-2.0`, `rWindowMax = REAL#2.0`

- **Explanation**: Receives the target value via an AR socket and colors the FillAttributes object `FillStyle_Bargraph_Mittelmarker_Gruen`, used exclusively by the triangle, green using Change Fill Attributes (ISO 11783-6 F.32) as long as the target value in the window is -2…+2, otherwise red. Since this FillAttributes object is used exclusively by the triangle (`Polygon_Bargraph_Mittelmarker`), the color change does not affect any other objects on the terminal.


### Sub-Blocks: `FillWindowFS_AR` (SubAppType)

`FillWindowFS_AR` was built as a separate `SubAppType`, modeled after `MyLib_AX-1.0.0\typelib\sys\GreenRedBackground1_AX.SUB`, instead of as `FBType` with an ST helper function—exclusively from existing generic adapter blocks, without any custom ST logic:

- **Split** (`AR_SPLIT_2`): distributes the incoming AR setpoint across two comparators.

- **WindowMinConst**/**WindowMaxConst** (`initval_AR`): provide the constant window boundaries (`rWindowMin`/`rWindowMax`) as AR values.

- **GE_Min** (`AR_GE`) checks `Sollwert >= rWindowMin`, **LE_Max** (`AR_LE`) checks `Sollwert <= rWindowMax`.

- **InWindow** (`AX_AND_2`) ANDs both comparison results to produce a Boolean value "in window?".

- **ColorSel** (`AX_SEL`, `IN0 = COLOR_RED`, `IN1 = COLOR_GREEN`) selects between red and green based on `InWindow`.

- **Inner** (`Q_FillAttributes`, `u8FillType = USINT#2`, `u16FillPatternId = ID_NULL`) sends the selected color as a change-fill attribute command (solid color, no pattern) to the VT object referenced by `u16ObjId`.

## Program Flow and Connections

1. The operator enters a target value at the terminal, which is provided as an AR adapter value via `Sollwert_N.rPhys`.

2. The adapter connection `Sollwert_N.rPhys → Split.IN` feeds the target value into the distributor `AR_SPLIT_4`.

3. `Split.OUT1 → Marker_Dreieck.rPhys` moves the triangle according to the target value.

4. `Split.OUT2 → SplitBar.rPhys` displays the same target value as a split bar graph.

5. `Split.OUT3 → Istwert_N.rPhys` writes the target value back to the terminal unchanged as the actual value.

6. `Split.OUT4 → MarkerColor.rPhys` feeds the same target value into the new window color logic.

7. Within `MarkerColor` (`FillWindowFS_AR`), the target value is distributed via `AR_SPLIT_2` to `AR_GE` (`>= -2.0`) and `AR_LE` (`<= +2.0`). The results are ANDed using `AX_AND_2`, and `AX_SEL` determines whether `COLOR_GREEN` or `COLOR_RED` is passed to `Q_FillAttributes`.

8. `Q_FillAttributes` sends a Change-Fill-Attributes command (ISO 11783-6 F.32) to the FillAttributes object `FillStyle_Bargraph_Mittelmarker_Gruen`, causing the fill color of the triangle to change live—green inside, red outside the window -2…+2.

## Summary

Exercise 228 extends the setup known from `Uebung_227_AX.md` (triangle, split-bar graph, actual value, all via AR adapter) by adding a fourth, independent point of consumption for the target value: a window-based color feedback that colors the triangle itself green or red. It demonstrates how Change Fill Attributes (ISO 11783-6 F.32) can be used to repurpose a single, exclusively used VT object as a status indicator, and how such window logic can be constructed solely from generic adapter components (Split, Compare, AND, Select) without having to write custom ST logic.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
