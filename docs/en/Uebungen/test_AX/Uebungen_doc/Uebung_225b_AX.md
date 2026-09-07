# Exercise_225b_AX: Triangle Setpoint Marker with PositionMarkerFSA

![Uebung_225b_AX_network](./Uebung_225b_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the adapter version of [Exercise 225b](../../test_B/Uebungen_doc/Uebung_225b.md) (test_B)]: identical function—a bracketed triangle marker follows a setpoint—but setpoint reading and actual value writing are handled via the AR adapter blocks `NumericValue_PHYSA`/`Q_NumericValue_PHYSA` instead of plain `REQ`/`IND` events. The triangle movement itself still uses the reusable composite block `PositionMarkerFS`, but is now addressed via its new adapter wrapper `PositionMarkerFSA`.

## Function Blocks (FBs) Used

- **Setpoint_N** (`isobus::UT::io::NumericValue::NumericValue_PHYSA`): AR adapter variant of `NumericValue_PHYS`.

- **Parameters**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`.

- **Explanation**: Reads the physical value of `InputNumber_Sollwert` and returns it as the AR adapter plug `rPhys`.


- **Split** (`adapter::events::unidirectional::AR_SPLIT_2`): Adapter distributor for a REAL adapter value.

- **Parameters**: None.

- **Explanation**: Cleanly distributes the single read setpoint to the two consumers `Marker_Dreieck` and `Istwert_N` — an adapter cannot point directly to multiple targets.

- **Actual Value_N** (`isobus::UT::Q::Q_NumericValue_PHYSA`): AR adapter variant of `Q_NumericValue_PHYS`.

- **Parameters**: `stObj = NumberVariable_Istwert_N`.

- **Explanation**: Writes the same setpoint back unchanged as the actual value, entirely via the AR adapter socket `rPhys`.


### Sub-Blocks: Marker_Triangle (`isobus::UT::Q::PositionMarkerFSA`)

`PositionMarkerFS` itself does not have an AR adapter interface. Just as `Q_NumericValue_PHYSA` wraps the block `Q_NumericValue_PHYS` around an AR socket, `PositionMarkerFSA` (`isobus::UT::Q`) now encapsulates `PositionMarkerFS` in the same way:

- **Parameters of the instance `Marker_Dreieck`**: `stObj = Container_PositionMarker`, `xScale = TRUE`.


**Parameters of the instance `Marker_Dreieck`**: `stObj = Container_PositionMarker`, `xScale = TRUE`.** - **Internal wiring** (within `.fbt` itself, not changed in this exercise): The AR adapter socket `rPhys` delivers the event (`E1`) and date (`D1`) directly to a single internal instance `Inner` of type `PositionMarkerFS` (`rPhys.E1 → Inner.REQ`, `rPhys.D1 → Inner.rValue`) — thus the bracket logic (min/max limit, center offset) is **not** duplicated, but reused unchanged. `Inner.xOver` and `Inner.xUnder` are passed out as separate AX adapter plugs (`xOver` and `xUnder`), exactly as with `Q_NumericValue_PHYSA`. `stObj` and `xScale` are passed through 1:1 to `Inner`.

- **Block file**: `Ventilsteuerung\4diacIDE-workspace\.lib\isobus-3.0.0\typelib\UT\Q\PositionMarkerFSA.fbt`.

The existing blocks `NumericValue_PHYSA`, `Q_NumericValue_PHYSA`, and `PositionMarkerFS` themselves were not modified for this wrapper.

## Program Flow and Connections

1. **Read Setpoint**: `Sollwert_N` reads `InputNumber_Sollwert` (via `NumberVariable_Sollwert_N`) and delivers it as an AR plug `rPhys`.

2. **Distribute**: `Sollwert_N.rPhys → Split.IN`. `Split` (`AR_SPLIT_2`) duplicates the value to `OUT1` and `OUT2`.

3. **Move Triangle**: `Split.OUT1 → Marker_Dreieck.rPhys`. Internally, `PositionMarkerFSA` handles the value as usual (min/max, center offset, `xScale`) and positions the triangle; if the value is exceeded, it reports this via its own `xOver`/`xUnder` adapter plugs (not wired further in this exercise).

4. **Write back actual value**: `Split.OUT2 → Istwert_N.rPhys`. The unchanged target value is written back as the actual value.

5. Everything runs exclusively via `<AdapterConnections>`—not a single plain event or data connection, just like with `Uebung_011b1_PHYSA`.


## Summary

Exercise 225b_AX reduces the pure adapter chain from 225_AX to the reuse of a proven composite component: Instead of wiring center addition, type conversion, and positioning as separate adapter components, `PositionMarkerFSA` takes over the complete bracket logic from `PositionMarkerFS` and exposes it via a single AR adapter socket. Functionally, the exercise is exactly the same as [Exercise 225b](../../test_B/Uebungen_doc/Uebung_225b.md) (test_B)] — only the wiring style (adapters instead of plain events) differs. The same wrapper pattern is reused in Exercise 226_AX for the split-bar graph (`BargraphSplitFS_AR`).



Exercise 225b_AX is used for the split-bar graph (`BargraphSplitFS_AR`). ---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
