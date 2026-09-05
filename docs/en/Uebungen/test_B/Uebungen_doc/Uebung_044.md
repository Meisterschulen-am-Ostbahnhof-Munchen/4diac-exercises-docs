# Uebung_044: Pure Time-Controlled Sequence, 5 Channels

This article describes the logiBUS® exercise `Uebung_044`. We build a purely time-driven step chain.

----

## Goal of the Exercise

Realize an automatic sequence across 5 outputs (Q1, Q2, Q3, Q4, Q5), where each step advances to the next purely after a fixed dwell time (`DT_S1_S2` etc., each `T#1s`) elapses - with no further button press needed once the sequence is running.

-----

## Description and Components

The subapplication `Uebung_044` uses the sequencer `sequence_ET_05_loop`, which cycles through 5 states (`S1` to `S5`) purely on elapsed time.

![Uebung_044_network](./Uebung_044_network.svg)

- **`sequence_ET_05_loop`**: Manages the 5 steps; each transition happens once the respective `DT_Sx_Sy` parameter elapses.
- **`E_TimeOut`**: Watchdog supervising the sequence.
- **`Q_NumericValue`**: Displays the current step number on the ISOBUS terminal.

-----

## Behavior

1. Start via button `I1` -> the sequence jumps to `S1`.
2. Each output (`Q1, Q2, Q3, Q4, Q5`) is switched active for the configured time before automatically advancing to the next step.
3. After the last step, the sequence automatically returns to S1 (ENDLOS/loop).
4. Reset via button `I4` -> everything off, sequence back to its base state.
