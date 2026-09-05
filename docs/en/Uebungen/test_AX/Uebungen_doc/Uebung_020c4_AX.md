# Uebung_020c4_AX: On-Delay with Adjustable, INI-Stored Time (AX)

This article describes the logiBUS® exercise `Uebung_020c4_AX`. This exercise corresponds to `Uebung_020c2_AX` (see there for the general behavior), but persists the operator-entered delay time to an INI file instead of NVS (ESP32 flash).

----

## Goal of the Exercise

`DigitalInput_I1` switches `DigitalOutput_Q1` only after an adjustable delay entered on the VT terminal. The time is persisted via `INI_IN_AND_STORE_AR` into an INI file (section `SECTION_I1_STORE`, key `KEY_I1_STORE`).

-----

## Description and Components

![Uebung_020c4_AX_network](./Uebung_020c4_AX_network.svg)

- **`DigitalInput_I1`**: `logiBUS::io::DI::logiBUS_IXA`.
- **`ATM_AX_TON`**: `adapter::events::unidirectional::timers::ATM_AX_TON`, AX adapter on-delay timer.
- **`AR_MULTIME`**: `adapter::iec61131::arithmetic::AR_MULTIME`, computes the actual `PT` (preset time) from the stored value.
- **`INI_IN_AND_STORE_AR`**: reads the operator-entered value and persists it to an INI file instead of NVS.
- **`DigitalOutput_Q1`**: `logiBUS::io::DQ::logiBUS_QXA`.

-----

## Behavior

1. The operator enters a numeric value on the ISOBUS terminal, which `INI_IN_AND_STORE_AR` immediately writes to the INI file.
2. `AR_MULTIME` computes the actual delay time (`PT`) for `ATM_AX_TON` from it.
3. Once `DigitalInput_I1` becomes active, `ATM_AX_TON` starts the delay.
4. Once the stored time elapses, `DigitalOutput_Q1` switches on.
5. The stored time survives a controller restart.
