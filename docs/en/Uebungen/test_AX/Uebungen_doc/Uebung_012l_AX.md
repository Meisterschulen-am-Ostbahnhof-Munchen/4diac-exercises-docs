# Uebung_012l_AX: String Input und Speichern NVS

This article describes the 4diac IDE sub-application Uebung_012l_AX (String Input und Speichern NVS).

----

![Uebung_012l_AX_network](./Uebung_012l_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **String Input und Speichern NVS**

-----

## Description and Components

The exercise consists of the sub-application Uebung_012l_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **StringValue_AIS**: Instance of type isobus::UT::io::StringValue::StringValue_AIS.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputString_S1
- **NVS_AIS**: Instance of type logiBUS::storage::esp32_nvs::NVS_AIS.
  - Parameter QI = TRUE
  - Parameter KEY = KEY_S1_STORE
  - Parameter DEFAULT_VALUE = STRING#'Test'
- **Q_StringValue_AIS**: Instance of type isobus::UT::Q::Q_StringValue_AIS.
  - Parameter u16ObjId = InputString_S1

### Connections and Interfaces

**Adapter Connections:**
- StringValue_AIS.IN -> NVS_AIS.AIS_IN
- NVS_AIS.AIS_OUT -> Q_StringValue_AIS.pau8String

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_012l_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
