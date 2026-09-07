# Exercise_227_AX: Triangle Setpoint Marker and Split Bar Graph from a Common Source

![Uebung_227_AX_network](./Uebung_227_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise combines Exercise 225b_AX (Triangle Setpoint Marker) and Exercise 226_AX (Split Bar Graph): **one** setpoint drives **both** displays simultaneously, as well as the actual value feedback. Unlike the two individual exercises, everything here runs consistently via AR adapter blocks—since Exercise 226_AX already reads its setpoint only via the AR adapter `NumericValue_PHYSA`, both devices must use the same wiring style (adapter) for a common source.


## Function Blocks (FBs) Used

- **Setpoint_N** (`isobus::UT::io::NumericValue::NumericValue_PHYSA`): AR adapter variant of `NumericValue_PHYS`.

- **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`.

- **Explanation**: Reads `NumberVariable_Sollwert_N` (the variable bound to `InputNumber_Sollwert`, VT object 9000) and returns the value as the AR adapter plug `rPhys`.

- **Split** (`adapter::events::unidirectional::AR_SPLIT_3`): Adapter distributor for a REAL adapter value to three targets.

- **Parameters**: None.

- **Explanation**: This cleanly distributes the single read setpoint to the three consumers `Marker_Dreieck`, `SplitBar`, and `Istwert_N` — an adapter cannot point directly to multiple destinations. Compared to `AR_SPLIT_2` in 225b_AX, a third output is added here.

- **Actual Value_N** (`isobus::UT::Q::Q_NumericValue_PHYSA`): AR adapter variant of `Q_NumericValue_PHYS`.

- **Parameter**: `stObj = NumberVariable_Istwert_N`.

- **Explanation**: Writes the same target value as the actual value to `NumberVariable_Istwert` — this drives both `InputNumber_Istwert` and the existing single bargraph pointer.


### Sub-Building Blocks: Marker_Triangle (`PositionMarkerFSA`) and SplitBar (`BargraphSplitFS_AR`)

This exercise does not introduce any new building block types, but combines two familiar wrappers from the preliminary exercises at the same source:

- **Marker_Triangle** (`isobus::UT::Q::PositionMarkerFSA`, `stObj = Container_PositionMarker`, `xScale = TRUE`): moves the triangle exactly as described in [Exercise 225b_AX](./Uebung_225b_AX.md)] — internally a single `PositionMarkerFS` instance with brackets and `xOver`/`xUnder` plugins.

- **SplitBar** (`isobus::UT::Q::BargraphSplitFS_AR`, `stObj = Bargraph_Split_BargraphSplit`): controls the split-bar graph exactly as described in [Exercise 226_AX](./Uebung_226_AX.md)] — internally a single `BargraphSplitFS` instance with brackets and `xOverRight`/`xOverLeft` plugins.

## Program Flow and Connections

1. **Read Setpoint**: `Sollwert_N` reads `NumberVariable_Sollwert_N` and returns the value as the AR plug `rPhys`.

2. **Distribute**: `Sollwert_N.rPhys → Split.IN`. `Split` (`AR_SPLIT_3`) duplicates the value to three independent outputs: `OUT1`, `OUT2`, and `OUT3`.

3. **Move Triangle**: `Split.OUT1 → Marker_Dreieck.rPhys`. The triangle (`Polygon_Bargraph_Mittelmarker` in the container `Container_PositionMarker`) follows the setpoint with bracketing and scaling (`xScale = TRUE`).

4. **Control Split Bar Graph**: `Split.OUT2 → SplitBar.rPhys`. Depending on the sign, the left or right bargraph (`Bargraph_Split_links`/`_rechts`) fills up according to the amount.

5. **Write back actual value**: `Split.OUT3 → Istwert_N.rPhys`. The same target value is written back unchanged as the actual value to `NumberVariable_Istwert`; this simultaneously drives `InputNumber_Istwert` and the existing single bargraph pointer.

6. Everything runs exclusively via `<AdapterConnections>`—not a single plain event or data connection.

The interaction is evident at the actual terminal: If the target value changes, the triangle appears, the split bargraph fills the appropriate side, and both the actual value field and the existing single bargraph pointer display the same value.

## Summary

Exercise 227_AX demonstrates how multiple independent VT displays can be fed from a single setpoint source without introducing new component types, provided all consumers are consistently addressed via AR adapters: `AR_SPLIT_3` distributes the single read value to `PositionMarkerFSA`, `BargraphSplitFS_AR`, and `Q_NumericValue_PHYSA` without duplicating the read logic. This exercise builds directly on 225b_AX and 226_AX and forms the basis for Exercise 228_AX, which additionally introduces a fourth use of the setpoint—a color logic for the triangle.


`AR_SPLIT_3` distributes the single read value to `PositionMarkerFSA`, `BargraphSplitFS_AR`, and `Q_NumericValue_PHYSA` without duplicating the read logic. This exercise builds directly on 225b_AX and 226_AX and forms the basis for Exercise 228_AX, which introduces a fourth use of the setpoint—a color logic for the triangle. ---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
