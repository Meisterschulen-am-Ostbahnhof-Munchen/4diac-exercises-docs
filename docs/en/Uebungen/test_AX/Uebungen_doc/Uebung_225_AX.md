# Exercise_225_AX: Triangle Setpoint Marker as a Pure Adapter Chain

![Uebung_225_AX_network](./Uebung_225_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the adapter version of [Exercise 225](../../test_B/Uebungen_doc/Uebung_225.md) (test_B): identical function—a triangle marker follows a setpoint offset by a fixed center point offset—but **completely** wired via adapters. There is no longer a single plain event or data connection in the entire sub-app; both the setpoint reading and actual value writing, as well as the complete calculation chain for the triangle movement, run via generic adapter blocks.

## Function Blocks (FBs) Used

- **Sollwert_N** (`isobus::UT::io::NumericValue::NumericValue_PHYSA`): AR adapter version of `NumericValue_PHYS`.

- **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`.

- **Explanation**: Reads the physical value of `InputNumber_Sollwert` and provides it as an AR adapter plug (`rPhys`) instead of a plain `rPhys` date.

- **Split** (`adapter::events::unidirectional::AR_SPLIT_2`): Adapter distributor for a REAL adapter value.

- **Parameters**: None.

- **Explanation**: An AR adapter socket may only be connected to exactly one source, but here the single read setpoint requires two consumers (actual value feedback and center addition). `AR_SPLIT_2` cleanly distributes the single AR input to `OUT1` and `OUT2`.

- **AR_ADD_2** (`adapter::iec61131::arithmetic::AR_ADD_2`): Adapter version of IEC 61131 addition.

- **Parameters**: None.

- **Explanation**: Adds two REAL adapter values (`IN1`, `IN2`) and outputs the sum as an AR adapter output `OUT`. Here: Setpoint + Center Offset.

- **initval_AR** (`adapter::types::unidirectional::AR::initval::initval_AR`): Constant encoder for an AR adapter value.

- **Parameter**: `INIT_VAL = REAL#42.0`.

- **Explanation**: Continuously returns the fixed center offset `42.0` as an AR adapter output—the adapter-native replacement for a plain REAL constant.

- **AR_TO_AI** (`adapter::conversion::unidirectional::AR_TO_AI`): Adapter type converter REAL → INT.

- **Parameter**: None.

- **Explanation**: Converts the AR adapter value (REAL) to an AI adapter value (INT)—the adapter-native equivalent of `F_REAL_TO_INT`.

- **initval_AI** (`adapter::types::unidirectional::AI::initval::initval_AI`): Constant generator for an AI adapter value.

- **Parameter**: `INIT_VAL = 0`.

- **Explanation**: Returns the fixed Y-position `0` as an AI adapter output.

- **Q_ChildPosition_Dreieck** (`isobus::UT::Q::Q_ChildPosition_AI`): Service block "Change Child Location" (ISO 11783-6) with AI adapter position inputs.

- **Parameters**: `u16ObjId = Polygon_Bargraph_Mittelmarker`, `u16ObjIdParent = Container_Sollwertmarker`, `xScale = TRUE`.

- **Explanation**: Moves the triangle object within its container. `s16Xposition` originates from the arithmetic chain (`AR_TO_AI.AI_OUT`), `s16Yposition` is hardwired to `0` via `initval_AI` — both as dedicated AI adapter sockets instead of plain INT inputs.

- **Istwert_N** (`isobus::UT::Q::Q_NumericValue_PHYSA`): AR adapter variant of `Q_NumericValue_PHYS`.

- **Parameter**: `stObj = NumberVariable_Istwert_N`.

- **Explanation**: Writes the unchanged setpoint back as the actual value, entirely via the AR adapter socket `rPhys` instead of a plain `REQ`/`rValue` pair.

### Sub-Blocks: None

This exercise uses only generic adapter blocks directly at the top level of the sub-app—no custom composite function block for the triangle movement, unlike the following exercises 225b_AX/226_AX/227_AX/228_AX, which use `PositionMarkerFSA` and `BargraphSplitFS_AR`, respectively.


## Program Flow and Connections

1. **Read Setpoint**: `Sollwert_N` reads `InputNumber_Sollwert` (via `NumberVariable_Sollwert_N`) and outputs it as an AR plug `rPhys`.

2. **Distribute**: `Sollwert_N.rPhys → Split.IN`. `Split` (`AR_SPLIT_2`) duplicates the value to two independent outputs, as one adapter socket cannot directly serve multiple destinations.

3. **Actual Value Branch**: `Split.OUT2 → Istwert_N.rPhys`. The setpoint is written back unchanged as the actual value.

4. **Center Addition**: `Split.OUT1 → AR_ADD_2.IN1`, `initval_AR.OUT (42.0) → AR_ADD_2.IN2`. `AR_ADD_2.OUT` returns `Sollwert + 42.0`.

5. **Type Conversion**: `AR_ADD_2.OUT → AR_TO_AI.AR_IN`. `AR_TO_AI` converts the REAL result to an INT adapter signal.

6. **Move Triangle**: `AR_TO_AI.AI_OUT → Q_ChildPosition_Dreieck.s16Xposition` sets the X position; `initval_AI.OUT (0) → Q_ChildPosition_Dreieck.s16Yposition` keeps the Y position constant. The triangle (`Polygon_Bargraph_Mittelmarker`) is thus shifted horizontally within the container `Container_Sollwertmarker` by the target value plus a 42-pixel center offset.

7. All connections run exclusively via `<AdapterConnections>`—there is not a single plain event or data connection.

## Summary

Exercise 225_AX demonstrates that a complete signal processing chain—reading, distributing, calculating, type converting, and writing—can be fully implemented using generic adapter components without writing a single custom composite component. Functionally, it is identical to the classic solution in [Exercise 225](../../test_B/Uebungen_doc/Uebung_225.md) (test_B), but differs fundamentally in its wiring style: Instead of loose event/data connections between individual function blocks, a continuous adapter pipeline is created, consisting of `AR_SPLIT_2`, `AR_ADD_2`, `initval_AR`, `AR_TO_AI`, and `initval_AI`. This exercise forms the basis for 225b_AX, which solves the same task with a reusable composite block (`PositionMarkerFSA`) instead of the loose adapter chain.



 ---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
