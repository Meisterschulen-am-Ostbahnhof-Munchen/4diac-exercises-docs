# Uebung_012m_sub: String Input, Persisted to INI via Subapp

This article describes the logiBUS® exercise `Uebung_012m_sub`. Text entered on the VT terminal is persisted to an INI file and displayed again after a restart.

----

## Goal of the Exercise

Accept a string value from the VT terminal, persist it to an INI file, and reload/display it from the INI file after a controller restart.

-----

## Description and Components

![Uebung_012m_sub_network](./Uebung_012m_sub_network.svg)

- **`StringValue_IS`**: `isobus::UT::io::StringValue::StringValue_IS`, text input field on the VT.
- **`NVS`**: `logiBUS::storage::esp32_nvs::NVS`, generic storage block (here parameterized, despite its name, for INI-file storage via `KEY`/`SECTION`).
- **`Q_StringValue`**: `isobus::UT::Q::Q_StringValue`, displays the persisted text on the VT terminal.

-----

## Behavior

1. On startup (`NVS.INITO`), the storage block reads the last persisted value (`NVS.GET`) and displays it in `Q_StringValue`.
2. When the operator changes the text in `StringValue_IS`, the new value is persisted immediately (`NVS.SET`).
3. After every write (`SETO`) and every read (`GETO`), the current value is forwarded to the display again (`IND`).
4. The text survives a controller restart.
