# Uebung_010f3: SoftKey_F1 ODER AuxFunction2_X1 auf DigitalOutput_Q1 mit GreenWhiteBackground (SoftKey UND Aux)

This article describes the 4diac IDE sub-application Uebung_010f3 (SoftKey_F1 ODER AuxFunction2_X1 auf DigitalOutput_Q1 mit GreenWhiteBackground (SoftKey UND Aux)).

----

![Uebung_010f3_network](./Uebung_010f3_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **SoftKey_F1 ODER AuxFunction2_X1 auf DigitalOutput_Q1 mit GreenWhiteBackground (SoftKey UND Aux)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_010f3.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Output = Output_Q1
- **SoftKey_F1**: Instance of type isobus::UT::io::Softkey::Softkey_IX.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
- **AuxFunction2_X1**: Instance of type isobus::UT::io::Auxiliary::IN::Aux_IX.
  - Parameter QI = TRUE
  - Parameter u16ObjId = AuxFunction2_X1
- **OR_2**: Instance of type iec61131::booleanOperators::OR_BOOL_2.

### Connections and Interfaces

**Event Connections:**

- SoftKey_F1.IND -> OR_2.REQ
- AuxFunction2_X1.IND -> OR_2.REQ
- OR_2.CNF -> DigitalOutput_Q1.REQ
- OR_2.CNF -> GreenWhiteBackground3.REQ

**Data Connections:**

- SoftKey_F1.IN -> OR_2.IN1
- AuxFunction2_X1.IN -> OR_2.IN2
- OR_2.OUT -> DigitalOutput_Q1.OUT
- OR_2.OUT -> GreenWhiteBackground3.DI1

### Notes from the Model

> SoftKey_F1 (VT) und AuxFunction2_X1 (Joystick) steuern denselben Q1 - ODER-verknuepft ueber OR_2. 
GreenWhiteBackground3 (nicht 1/2 einzeln!) bedient beide Hintergruende aus einem Baustein: u16ObjId=SoftKey (VT) und u16ObjIdA=Aux (VT+Aux-Handle).

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_010f3 provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
