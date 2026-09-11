# Uebung_006a2b: 2x SR und T-Flip-Flop mit IX

This article describes the 4diac IDE sub-application Uebung_006a2b (2x SR und T-Flip-Flop mit IX).

----

![Uebung_006a2b_network](./Uebung_006a2b_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **2x SR und T-Flip-Flop mit IX**

-----

## Description and Components

The exercise consists of the sub-application Uebung_006a2b.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I1
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I2
- **E_T_FF_SR_Q1**: Instance of type logiBUS::bistableElements::FB_RS_T_FF.
- **DigitalInput_CLK_I3**: Instance of type logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I3
- **E_T_FF_SR_Q2**: Instance of type logiBUS::bistableElements::FB_RS_T_FF.
- **DigitalOutput_Q2**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2

### Connections and Interfaces

**Event Connections:**

- DigitalInput_CLK_I1.IND -> E_T_FF_SR_Q1.REQ
- DigitalInput_CLK_I2.IND -> E_T_FF_SR_Q2.REQ
- DigitalInput_CLK_I3.IND -> E_T_FF_SR_Q1.REQ
- DigitalInput_CLK_I3.IND -> E_T_FF_SR_Q2.REQ
- E_T_FF_SR_Q1.CNF -> DigitalOutput_Q1.REQ
- E_T_FF_SR_Q2.CNF -> DigitalOutput_Q2.REQ

**Data Connections:**

- DigitalInput_CLK_I1.IN -> E_T_FF_SR_Q1.CLK
- DigitalInput_CLK_I2.IN -> E_T_FF_SR_Q2.CLK
- DigitalInput_CLK_I3.IN -> E_T_FF_SR_Q1.R1
- DigitalInput_CLK_I3.IN -> E_T_FF_SR_Q2.R1
- E_T_FF_SR_Q1.Q1 -> DigitalOutput_Q1.OUT
- E_T_FF_SR_Q2.Q1 -> DigitalOutput_Q2.OUT

### Notes from the Model

> Hausmeister-Aus (alles Aus mit einem Druck)

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_006a2b provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
