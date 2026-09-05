# Uebung_020c2_AX: On-Delay with Adjustable, NVS-Stored Time (AX)

This article describes the logiBUS® exercise `Uebung_020c2_AX`. We build an on-delay circuit whose delay time is set via the ISOBUS terminal and persisted to ESP32 flash storage.

----

## Goal of the Exercise

`DigitalInput_I1` switches `DigitalOutput_Q1` only after an adjustable delay (`E_TON`, here as the AX adapter `ATM_AX_TON`). The delay time is adjustable on the VT terminal and is persisted via NVS (Non-Volatile Storage, ESP32 flash), so it survives a restart.

-----

## Description and Components

![Uebung_020c2_AX_network](./Uebung_020c2_AX_network.svg)

- **`DigitalInput_I1`**: `logiBUS::io::DI::logiBUS_IXA`, physical button/switch.
- **`ATM_AX_TON`**: `adapter::events::unidirectional::timers::ATM_AX_TON`, the AX adapter variant of the on-delay timer `E_TON`.
- **`AR_MULTIME`**: `adapter::iec61131::arithmetic::AR_MULTIME`, multiplies the operator-entered value by a time base to compute the timer's actual `PT` (preset time).
- **`NVS_IN_AND_STORE_AR`**: reads the numeric value entered by the operator on the VT and persists it to ESP32 flash (`KEY_I1_STORE`).
- **`DigitalOutput_Q1`**: `logiBUS::io::DQ::logiBUS_QXA`.

-----

## Behavior

1. The operator enters a numeric value on the ISOBUS terminal, which `NVS_IN_AND_STORE_AR` immediately persists to ESP32 flash.
2. `AR_MULTIME` computes the actual delay time (`PT`) for `ATM_AX_TON` from it.
3. Once `DigitalInput_I1` becomes active, `ATM_AX_TON` starts the delay.
4. Once the (stored) time elapses, `DigitalOutput_Q1` switches on.
5. The stored time survives a controller restart.
