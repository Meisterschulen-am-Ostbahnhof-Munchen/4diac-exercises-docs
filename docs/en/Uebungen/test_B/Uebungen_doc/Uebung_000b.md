# Uebung_000b: AND

This article describes the 4diac IDE sub-application Uebung_000b (AND).

----

![Uebung_000b_network](./Uebung_000b_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **AND**

-----

## Description and Components

The exercise consists of the sub-application Uebung_000b.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **AND_2**: Instance of type iec61131::booleanOperators::AND_BOOL_2.
  - Parameter IN1 = TRUE
  - Parameter IN2 = TRUE
- **INIT**: Instance of type iec61131::booleanOperators::INIT.

### Connections and Interfaces

**Event Connections:**

- INIT.INITO -> INIT.REQ
- INIT.CNF -> AND_2.REQ

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_000b provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
