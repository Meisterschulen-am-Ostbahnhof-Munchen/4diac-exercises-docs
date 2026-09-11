# Uebung_009a_AX: RampLimitFS mit AUDI_RampLimitFS Wrapper

This article describes the 4diac IDE sub-application Uebung_009a_AX (RampLimitFS mit AUDI_RampLimitFS Wrapper).

----

![Uebung_009a_AX_network](./Uebung_009a_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **RampLimitFS mit AUDI_RampLimitFS Wrapper**

-----

## Description and Components

The exercise consists of the sub-application Uebung_009a_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **Q_NumericValue**: Instance of type isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = OutputNumber_N1
- **UP_FAST**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_LONG_PRESS_START
- **DOWN_SLOW**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **ZERO**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DOWN_FAST**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_LONG_PRESS_START
- **UP_SLOW**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **RampLimitFS**: Instance of type adapter::signalprocessing::ramp::AUDI_RampLimitFS.
  - Parameter VAL_ZERO = DINT#0
  - Parameter SLOW = DINT#1
  - Parameter FAST = DINT#10
  - Parameter VAL_FULL = DINT#100
- **FULL**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK

### Connections and Interfaces

**Adapter Connections:**

- RampLimitFS.OUT -> Q_NumericValue.u32NewValue

**Event Connections:**

- UP_FAST.IND -> RampLimitFS.UP_FAST
- FULL.IND -> RampLimitFS.FULL
- ZERO.IND -> RampLimitFS.ZERO
- DOWN_SLOW.IND -> RampLimitFS.DOWN_SLOW
- DOWN_FAST.IND -> RampLimitFS.DOWN_FAST
- UP_SLOW.IND -> RampLimitFS.UP_SLOW

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_009a_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
