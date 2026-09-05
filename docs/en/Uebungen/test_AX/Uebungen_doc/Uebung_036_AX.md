# Uebung_036_AX: Mirror Sequence V2 with Step Chain

This article describes the logiBUS® exercise `Uebung_036_AX`. We build a 4-channel step chain using AX adapter technology, where each step is triggered manually via a button.

----

## Goal of the Exercise

Realize a sequence across 4 outputs (`Q1`-`Q4`), where each transition is triggered by its own button (`I1`-`I4`) - unlike the time-driven variants (e.g. `Uebung_037_AX`), nothing here advances automatically.

-----

## Description and Components

The subapplication `Uebung_036_AX` uses the combined sequencer `sequence_ET_04_AX`, which has both event and time inputs. In this exercise all four transitions (`START_S1`, `S1_S2`, `S2_S3`, `RESET`) are each wired directly to a button - the time parameters (`DT_S1_S2`/`DT_S2_S3` = `NO_TIME`, `DT_S3_S4`/`DT_S4_START` = `T#2s`) only act as a fallback for the last two transitions, which no longer have a dedicated button.

![Uebung_036_AX_network](./Uebung_036_AX_network.svg)

- **`sequence_ET_04_AX`**: 4-step sequencer, used here predominantly event-driven.
- **`Q_NumericValue_AUDI`**: Displays the current step number on the ISOBUS terminal.

-----

## Behavior

1. Start via button `I1` -> the sequence jumps to `S1`, `Q1` becomes active.
2. Button `I2` -> transition to `S2`.
3. Button `I3` -> transition to `S3`.
4. The transition to `S4` and back to the start happens automatically after `T#2s`, since no dedicated button is wired for it.
5. Reset via button `I4` -> everything off.
