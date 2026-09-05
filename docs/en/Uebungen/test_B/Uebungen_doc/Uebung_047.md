# Uebung_047: Pure Event-Driven Step Chain, 4 Channels

This article describes the logiBUS® exercise `Uebung_047`. We build a purely event-driven step chain.

----

## Goal of the Exercise

Realize a sequence across 4 outputs (Q1, Q2, Q3, Q4), where each step advances solely on an external event (a button press) - no automatic time-based advancement.

-----

## Description and Components

The subapplication `Uebung_047` uses the sequencer `sequence_E_04`, which cycles through 4 states purely event-driven.

![Uebung_047_network](./Uebung_047_network.svg)

Each transition between steps is triggered by its own dedicated button press (`I1` through `I4`) - the sequence waits at each step until the next button is pressed.

- **`Q_NumericValue`**: Displays the current step number on the ISOBUS terminal.

-----

## Behavior

1. Start via button `I1` -> the sequence jumps to `S1`, output `Q1` becomes active.
2. Every further button press advances to the next step.
3. After the last step, the sequence stays put until it is reset.
4. Reset via the last button -> everything off.
