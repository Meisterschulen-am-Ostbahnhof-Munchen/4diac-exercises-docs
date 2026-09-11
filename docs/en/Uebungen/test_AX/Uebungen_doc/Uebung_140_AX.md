# Uebung_140_AX: Übung zu SYS_ONTIME (Betriebsstundenzähler)

This article describes the 4diac IDE sub-application Uebung_140_AX (Übung zu SYS_ONTIME (Betriebsstundenzähler)).

----

![Uebung_140_AX_network](./Uebung_140_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu SYS_ONTIME (Betriebsstundenzähler)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_140_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **SYS_ONTIME**: Instance of type logiBUS::signalprocessing::measurement::SYS_ONTIME.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Signal Forwarding**: Function blocks process control and data signals according to their configuration.

-----

## Summary

Exercise Uebung_140_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
