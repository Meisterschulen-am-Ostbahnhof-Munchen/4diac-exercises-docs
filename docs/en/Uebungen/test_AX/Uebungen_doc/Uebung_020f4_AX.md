# Uebung_020f4_AX: Blink Light on a DataPanel Output (AX)

This article describes the logiBUS® exercise `Uebung_020f4_AX`. We build a simple blinker on a DataPanel output.

----

## Goal of the Exercise

A DataPanel output (`DigitalOutput_1A`) blinks continuously with fixed on/off times once it has been initialized.

-----

## Description and Components

![Uebung_020f4_AX_network](./Uebung_020f4_AX_network.svg)

- **`DigitalOutput_1A`**: `DataPanel::io::MI::DQ::DataPanel_MI_QXA`, output on the DataPanel expansion module (`u8SAMember = MI_00`).
- **`AX_BLINK`**: `adapter::events::unidirectional::signals::AX_BLINK`, generates a periodic on/off signal with `TIMELOW = T#1s` and `TIMEHIGH = T#1s200ms`.

-----

## Behavior

1. Once the output initializes (`DigitalOutput_1A.INITO`), `AX_BLINK` starts.
2. `AX_BLINK` alternately switches the output on for `T#1s200ms` and off for `T#1s` (blinking).
3. The AX adapter forwards the blink state directly to `DigitalOutput_1A.OUT`.
