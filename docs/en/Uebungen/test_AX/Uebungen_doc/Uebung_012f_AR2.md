# Uebung_012f_AR2: Numeric Value Input via VT, Persisted to NVS via AR2 via Subapp

This article describes the logiBUS® exercise `Uebung_012f_AR2`. Part of a series of exercises (`Uebung_012d_AR2` through `Uebung_012o_AR2`) demonstrating different combinations of input source (VT field, OPC-UA, or both) and storage target (NVS flash or INI file) via the bidirectional `AR2` adapter.

----

## Goal of the Exercise

Accept a numeric value from a VT numeric field (`NumericValue_TO_AR2`) and persist it NVS (ESP32 flash) - using the bidirectional AR2 adapter (as a ready-made encapsulated subapp).

-----

## Description and Components

![Uebung_012f_AR2_network](./Uebung_012f_AR2_network.svg)

- **`NVS_IN_AND_STORE_AR2 (Subapp)`**: encapsulates VT/OPC-UA input, the AR2 adapter, and persistence NVS (ESP32 flash) as a single, self-contained block - the internal wiring isn't visible as a separate FB network, it's fully encapsulated inside the block itself.

-----

## Behavior

The numeric value, coming from a VT numeric field (`NumericValue_TO_AR2`), is processed internally by `NVS_IN_AND_STORE_AR2 (Subapp)` and persisted NVS (ESP32 flash), without the individual processing steps appearing as separate FB instances in this subapplication.
