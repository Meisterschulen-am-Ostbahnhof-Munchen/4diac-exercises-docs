# Uebung_074a_AUI: Output RPTO on the UT (Adapter Version) with Fendt Fallback, FIX

This article describes the logiBUS® exercise `Uebung_074a_AUI`. We read the ISOBUS TECU rear PTO output shaft speed (RPTO) and display it on the ISOBUS terminal, with an additional Fendt-specific fallback selection.

----

## Goal of the Exercise

Display the rear PTO output shaft speed on the VT terminal, with the ability to switch between the real TECU value and a fixed fallback value (`FIX`) - relevant for Fendt tractors, where the TECU signal is not reliable under certain conditions.

-----

## Description and Components

![Uebung_074a_AUI_network](./Uebung_074a_AUI_network.svg)

- **`IA_RPTO`**: `isobus::tecu::IA_RPTO`, reads the PTO output shaft speed from the Tractor ECU (TECU) via ISOBUS.
- **`AUI_AX_SEL_AUI`**: `adapter::iec61131::selection::AUI_AX_SEL_AUI`, selects between the real TECU value (`IN0`) and a fixed fallback value `UINT#0` (`IN1`) - controlled (`G`) by `IA_RPTO`'s timeout signal.
- **`CONST_ZERO`**: `adapter::conversion::unidirectional::AUI_UINT_TO_UI`, provides the fixed fallback value `UINT#0` as an AUI adapter value.
- **`CONV_AUI_AUDI`**: `adapter::conversion::unidirectional::AUI_TO_AUDI`, converts the selected AUI value into the AUDI format needed by `Q_NumericValue`.
- **`Q_NumericValue_PTO`**: displays the value on the VT terminal (`u16ObjId = NumberVariable_Rear_PTO_output_shaft_speed`).

-----

## Behavior

1. `IA_RPTO` continuously reads the PTO output shaft speed from the TECU.
2. If `IA_RPTO` reports a timeout (`TIMEOUT`), `AUI_AX_SEL_AUI` switches to the fixed fallback value `UINT#0`; otherwise the real speed value (`SPEED`) is passed through.
3. The selected value is converted via `CONV_AUI_AUDI` into the display format and shown in `Q_NumericValue_PTO`.
4. On every initialization (`INITO`), the display and fallback value are re-prepared.
