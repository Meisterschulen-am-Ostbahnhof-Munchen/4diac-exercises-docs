# Uebung_012i_AR2: Numeric Value Input (no VT field), Persisted to INI via AR2 (bidirectional, direct)

This article describes the logiBUS® exercise `Uebung_012i_AR2`. Part of a series of exercises (`Uebung_012d_AR2` through `Uebung_012o_AR2`) demonstrating different combinations of input source (VT field, OPC-UA, or both) and storage target (NVS flash or INI file) via the bidirectional `AR2` adapter.

----

## Goal of the Exercise

Accept a numeric value exclusively via OPC-UA (`OPC_TO_AR2`) - there is no VT numeric field on the mask and persist it an INI file - using the bidirectional AR2 adapter (wired directly, no subapp encapsulation).

-----

## Description and Components

![Uebung_012i_AR2_network](./Uebung_012i_AR2_network.svg)

- **`INI_AR2`**: `eclipse4diac::storage::INI_AR2`, bidirectional AR2 adapter block that persists the value directly.

-----

## Behavior

The numeric value, coming exclusively via OPC-UA (`OPC_TO_AR2`) - there is no VT numeric field on the mask, is passed directly through the bidirectional `AR2` adapter to `INI_AR2`, which persists it an INI file - with no intermediate subapp.
