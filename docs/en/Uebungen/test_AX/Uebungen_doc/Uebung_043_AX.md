# Uebung_043_AX: Scaling Function Block with limits Testing

This article describes the 4diac IDE sub-application Uebung_043_AX (Scaling Function Block with limits Testing).

----

![Uebung_043_AX_network](./Uebung_043_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Scaling Function Block with limits Testing**

-----

## Description and Components

The exercise consists of the sub-application Uebung_043_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **SCALE_LIM**: Instance of type eclipse4diac::signalprocessing::SCALE_LIM.
  - Parameter IN = 50.0
  - Parameter MAX_IN = 100.0
  - Parameter MIN_IN = 0.0
  - Parameter MAX_IN_LIM = 99.0
  - Parameter MIN_IN_LIM = 1.0
  - Parameter MAX_OUT = 85.0
  - Parameter MIN_OUT = 30.0
  - Parameter MAX_OUT_FIX = 100.0
  - Parameter MIN_OUT_FIX = 0.0

### Connections and Interfaces

**Event Connections:**
- DigitalInput_CLK_I1.IND -> SCALE_LIM.REQ

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_043_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
