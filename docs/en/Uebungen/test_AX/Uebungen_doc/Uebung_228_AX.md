# Uebung_228_AX: Window Colour Feedback on the Triangle (Change Fill Attributes, ISO 11783-6 F.32)

![Uebung_228_AX_network](./Uebung_228_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is derived from `Uebung_227_AX.md`: the set value still moves a triangle (position marker) and a split bargraph and is written back as actual value, all via AR adapters. In addition, the triangle is coloured green inside the window -2…+2 and red outside. The exercise thus demonstrates the use of Change Fill Attributes (ISO 11783-6, F.32) as a simple status indicator on the VT terminal.

## Function Blocks (FBs) Used

- **Sollwert_N**: Terminal input for the set value
    - **Type**: [`isobus::UT::io::NumericValue::NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_PHYSA/)
    - **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`
    - **Explanation**: Reads the set value entered by the operator from the VT terminal and provides it as a physical value (`rPhys`, AR adapter).
- **Split**: Distributor for the set value
    - **Type**: [`adapter::events::unidirectional::AR_SPLIT_4`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/REAL/AR_SPLIT_4/)
    - **Parameters**: none
    - **Explanation**: Distributes the single incoming AR set value to four consumers (one more than in `Uebung_227_AX.md`): triangle position, split bargraph, actual value write-back and the colour logic.
- **Marker_Dreieck**: Position marker (triangle) on the terminal
    - **Type**: [`isobus::UT::Q::PositionMarkerFSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/PositionMarkerFSA/)
    - **Parameters**: `stObj = Container_PositionMarker`, `xScale = TRUE`
    - **Explanation**: Positions the triangle object according to the set value.
- **SplitBar**: Split bargraph on the terminal
    - **Type**: [`isobus::UT::Q::BargraphSplitFS_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/BargraphSplitFS_AR/)
    - **Parameters**: `stObj = Bargraph_Split_BargraphSplit`
    - **Explanation**: Additionally displays the set value as a split bargraph.
- **Istwert_N**: Terminal output of the actual value
    - **Type**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameters**: `stObj = NumberVariable_Istwert_N`
    - **Explanation**: Writes the set value (passed through unchanged) back to the terminal as actual value.
- **MarkerColor**: Window colour logic for the triangle
    - **Type**: `MyLib::sys::FillWindowFS_AR` (SubApp instance)
    - **Parameters**: `u16ObjId = FillStyle_Bargraph_Mittelmarker_Gruen`, `rWindowMin = REAL#-2.0`, `rWindowMax = REAL#2.0`
    - **Explanation**: Receives the set value via an AR socket and colours the FillAttributes object `FillStyle_Bargraph_Mittelmarker_Gruen` green via Change Fill Attributes (ISO 11783-6 F.32) as long as the set value is within the window -2…+2, otherwise red.

### Sub-Blocks: `FillWindowFS_AR` (SubAppType)

`FillWindowFS_AR` (`MyLib_AX-1.0.0\typelib\sys\FillWindowFS_AR.SUB`, version 2.0) colours a FillAttributes object green inside `[rWindowMin, rWindowMax]` and red outside. It consists exclusively of existing blocks, without custom ST logic:

- **WindowMinConst**/**WindowMaxConst** (`initval_AR`): provide the window limits `rWindowMin`/`rWindowMax` as AR values.
- **CENTER_CMP** (`AR_DEADBAND_COMPARE`): receives the set value (`rPhys` at `Y`) and the limits (`WINDOW_MIN`, `WINDOW_MAX`) and provides `CENTERED` for "inside the window".
- **ColorSel** (`AX_SEL`, `IN0 = COLOR_RED`, `IN1 = COLOR_GREEN`): selects between red and green based on `CENTERED` (at `G`).
- **Inner** (`Q_FillAttributes`, `u8FillType = USINT#2`, `u16FillPatternId = ID_NULL`): sends the selected colour (`u8FillColour`) as a Change Fill Attributes command to the VT object referenced via `u16ObjId`.

## Program Flow and Connections

1. `Sollwert_N.rPhys` → `Split.IN` (AdapterConnection): The set value feeds the distributor `AR_SPLIT_4`.
2. `Split.OUT1` → `Marker_Dreieck.rPhys`: moves the triangle according to the set value.
3. `Split.OUT2` → `SplitBar.rPhys`: additionally displays the same set value as a split bargraph.
4. `Split.OUT3` → `Istwert_N.rPhys`: writes the set value back unchanged to the terminal as actual value.
5. `Split.OUT4` → `MarkerColor.rPhys`: feeds the same set value into the window colour logic.
6. Inside `MarkerColor` (`FillWindowFS_AR`), `AR_DEADBAND_COMPARE` compares the set value with the window `-2.0…+2.0`; `ColorSel` selects `COLOR_GREEN` or `COLOR_RED`, and `ColorSel.CNF` triggers `Q_FillAttributes.REQ`.
7. `Q_FillAttributes` sends a Change Fill Attributes command (ISO 11783-6 F.32) to `FillStyle_Bargraph_Mittelmarker_Gruen`, so the fill colour of the triangle changes live: green inside, red outside the window -2…+2.

## Summary

Exercise 228 extends the structure known from `Uebung_227_AX.md` (triangle, split bargraph, actual value, all via AR adapters) by a fourth consumer of the set value: a window-based colour feedback that colours the triangle green or red. It shows how Change Fill Attributes (ISO 11783-6 F.32) can be used as a status indicator and how the window logic can be assembled as a sub-app from existing adapter blocks, without custom ST logic.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
