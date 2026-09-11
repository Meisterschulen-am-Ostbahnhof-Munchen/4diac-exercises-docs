# Uebung_042_AX: Scaling Function Block Testing

This article describes the 4diac IDE sub-application Uebung_042_AX (Scaling Function Block Testing).

----

![Uebung_042_AX_network](./Uebung_042_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Scaling Function Block Testing**

-----

## Description and Components

The exercise consists of the sub-application Uebung_042_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **SCALE**: Instance of type eclipse4diac::signalprocessing::SCALE.
  - Parameter IN = 10.0
  - Parameter MAX_IN = 20.0
  - Parameter MIN_IN = 4.0
  - Parameter MAX_OUT = 100.0
  - Parameter MIN_OUT = 0.0

### Connections and Interfaces

**Event Connections:**
- DigitalInput_CLK_I1.IND -> SCALE.REQ

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_042_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
