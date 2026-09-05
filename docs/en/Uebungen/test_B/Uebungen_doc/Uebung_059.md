# Uebung_059: Pure Event-Driven Step Chain, 8 Channels

This article describes the logiBUS® exercise `Uebung_059`. We build a purely event-driven step chain.

----

## Goal of the Exercise

Realize a sequence across 8 outputs (Q1, Q2, Q3, Q4, Q5, Q6, Q7, Q8), where each step advances solely on an external event (a button press) - no automatic time-based advancement.

-----

## Description and Components

The subapplication `Uebung_059` uses the sequencer `sequence_E_08`, which cycles through 8 states purely event-driven.

![Uebung_059_network](./Uebung_059_network.svg)

Since only the physical buttons `I1`-`I4` are available but up to 8 transitions are needed, an `E_CTU` counter tallies presses of `I2`/`I3`, and a downstream `E_DEMUX` fans that count out to the sequencer's individual `Sx_Sy` transitions - so 2 buttons can trigger 8 state transitions without needing one dedicated button per step.

- **`Q_NumericValue`**: Displays the current step number on the ISOBUS terminal.

-----

## Behavior

1. Start via button `I1` -> the sequence jumps to `S1`, output `Q1` becomes active.
2. Every further button press advances to the next step.
3. After the last step, the sequence stays put until it is reset.
4. Reset via the last button -> everything off.
