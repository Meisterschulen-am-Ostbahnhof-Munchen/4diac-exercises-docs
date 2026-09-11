# Uebung_081_AX: Beispiel für E_CTD, mit Plug and Socket

This article describes the 4diac IDE sub-application Uebung_081_AX (Beispiel für E_CTD, mit Plug and Socket).

----

![Uebung_081_AX_network](./Uebung_081_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Beispiel für E_CTD, mit Plug and Socket**

-----

## Description and Components

The exercise consists of the sub-application Uebung_081_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_CTD**: Instance of type iec61499::events::E_CTD.
  - Parameter PV = UINT#5
- **DigitalInput_CLK_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AX_BOOL_TO_X**: Instance of type adapter::conversion::unidirectional::AX_BOOL_TO_X.

### Connections and Interfaces

**Adapter Connections:**

- AX_BOOL_TO_X.AX_OUT -> DigitalOutput_Q1.OUT

**Event Connections:**

- DigitalInput_CLK_I1.IND -> E_CTD.CD
- E_CTD.CDO -> AX_BOOL_TO_X.REQ
- E_CTD.LDO -> AX_BOOL_TO_X.REQ
- DigitalInput_CLK_I2.IND -> E_CTD.LD

**Data Connections:**

- E_CTD.Q -> AX_BOOL_TO_X.OUT

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_081_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
