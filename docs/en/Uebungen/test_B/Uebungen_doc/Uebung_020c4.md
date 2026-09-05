# Uebung_020c4: On-Delay with Adjustable, INI-Stored Time

This article describes the logiBUS® exercise `Uebung_020c4`. The direct (non-AX-adapter) variant of `Uebung_020c4_AX` - see there for the conceptually identical behavior.

----

## Goal of the Exercise

`DigitalInput_I1` switches `DigitalOutput_Q1` only after an adjustable delay (`E_TON`). The delay time is adjustable on the VT terminal and is persisted to an INI file (section `SECTION_I1_STORE`, key `KEY_I1_STORE`).

-----

## Description and Components

![Uebung_020c4_network](./Uebung_020c4_network.svg)

- **`DigitalInput_I1`**: `logiBUS::io::DI::logiBUS_IX`.
- **`E_TON`**: `iec61499::events::timers::E_TON`, on-delay timer.
- **`F_MULTIME`**: `iec61131::arithmetic::F_MULTIME`, computes the actual `PT` (preset time) from the stored value.
- **`INI_IN_AND_STORE_UDINT`**: reads the operator-entered value and persists it to the INI file.
- **`DigitalOutput_Q1`**: `logiBUS::io::DQ::logiBUS_QX`.

-----

## Behavior

1. The operator enters a numeric value on the ISOBUS terminal, which `INI_IN_AND_STORE_UDINT` immediately writes to the INI file.
2. `F_MULTIME` computes the actual delay time (`PT`) for `E_TON` from it.
3. Once `DigitalInput_I1` becomes active (`IND`), `E_TON` starts the delay.
4. Once the stored time elapses, `DigitalOutput_Q1` switches on (`REQ`).
5. The stored time survives a controller restart.
