# Uebung_010a5: SoftKey_F1 to DigitalOutput_Q1 (DataPanel)

This article describes the logiBUS® exercise `Uebung_010a5`. A softkey on the ISOBUS terminal switches a DataPanel output.

----

## Goal of the Exercise

The softkey `SoftKey_F1` on the VT terminal directly switches a digital output on the DataPanel expansion module.

-----

## Description and Components

![Uebung_010a5_network](./Uebung_010a5_network.svg)

- **`SoftKey_F1`**: `isobus::UT::io::Softkey::Softkey_IX`, reads the state of the softkey `SoftKey_F1` from the VT.
- **`Input_Power_Port_5`**: `DataPanel::io::MI::DQ::DataPanel_MI_QX`, digital output on the DataPanel (`u8SAMember = MI_00`).

-----

## Behavior

1. The operator presses `SoftKey_F1` on the VT terminal.
2. The softkey's state (`SoftKey_F1.IN`) is passed directly through to the DataPanel output (`Input_Power_Port_5.OUT`).
3. Every state change (`IND`) triggers a request (`REQ`) on the output.
