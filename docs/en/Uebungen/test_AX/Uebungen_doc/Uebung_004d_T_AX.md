# Uebung_004d_T_AX: Exercise for FB_T_FF (Toggle Flip-Flop)

This article describes the 4diac IDE sub-application Uebung_004d_T_AX (Exercise for FB_T_FF (Toggle Flip-Flop)).

----

![Uebung_004d_T_AX_network](./Uebung_004d_T_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Exercise for FB_T_FF (Toggle Flip-Flop)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_004d_T_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_RST**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_CLK**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **T_FF**: Instance of type adapter::bistableElements::AX_FB_T_FF.
- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1

### Connections and Interfaces

**Adapter Connections:**
- DigitalInput_RST.IN -> T_FF.RST
- DigitalInput_CLK.IN -> T_FF.CLK
- T_FF.Q1 -> DigitalOutput_Q1.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_004d_T_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
