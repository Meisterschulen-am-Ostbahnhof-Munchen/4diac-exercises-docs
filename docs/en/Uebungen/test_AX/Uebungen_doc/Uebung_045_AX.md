# Uebung_045_AX: Combined Event/Time Sequence Control, 8 Channels (Adapter Version)

This article describes the logiBUS® exercise `Uebung_045_AX`. We build a combined event- and time-driven step chain using AX adapter technology.

----

## Goal of the Exercise

Realize a sequence across 8 outputs (Q1, Q2, Q3, Q4, Q5, Q6, Q7, Q8), started and reset via events (buttons), while the individual steps advance further via fixed dwell times (`DT_Sx_Sy`, each `T#1s`) - a combination of button-driven start/reset and automatic timed advancement between steps.

-----

## Description and Components

The subapplication `Uebung_045_AX` uses the combined sequencer `sequence_ET_08_AX`.

![Uebung_045_AX_network](./Uebung_045_AX_network.svg)

- **`sequence_ET_08_AX`**: Starts on an event (`I1`), then advances the 8 steps on elapsed time.
- **`Q_NumericValue_AUDI`**: Displays the current step number on the ISOBUS terminal.

-----

## Behavior

1. Start via button `I1` -> the sequence jumps to `S1`.
2. Each output (`Q1, Q2, Q3, Q4, Q5, Q6, Q7, Q8`) is switched active for the configured time before automatically advancing to the next step.
3. After the last step, the sequence ends.
4. Reset via button `I4` -> everything off.
