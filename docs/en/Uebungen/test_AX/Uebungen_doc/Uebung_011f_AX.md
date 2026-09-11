# Uebung_011f_AX: Numeric Value Input I3 Durchschleifen auf N3 (Input und Output PHYS via NumericObjectPool_S)

This article describes the 4diac IDE sub-application Uebung_011f_AX (Numeric Value Input I3 Durchschleifen auf N3 (Input und Output PHYS via NumericObjectPool_S)).

----

![Uebung_011f_AX_network](./Uebung_011f_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Numeric Value Input I3 Durchschleifen auf N3 (Input und Output PHYS via NumericObjectPool_S)**

-----

## Description and Components

The exercise consists of the sub-application Uebung_011f_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **NumericValue_PHYS**: Instance of type isobus::UT::io::NumericValue::NumericValue_PHYSA.
  - Parameter stObj = InputNumber_I3_N
- **Q_NumericValue_PHYS**: Instance of type isobus::UT::Q::Q_NumericValue_PHYSA.
  - Parameter stObj = OutputNumber_N3_N

### Connections and Interfaces

**Adapter Connections:**

- NumericValue_PHYS.rPhys -> Q_NumericValue_PHYS.rPhys

### Notes from the Model

> Beispiel: I3-Eingabe -500.00 → rPhys=-500.0 → Q_NumericValue_PHYS(N3) → N3 zeigt -500.00.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_011f_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
