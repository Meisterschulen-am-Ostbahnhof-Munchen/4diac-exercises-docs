# Uebung_135: Exercise on ISOBUS Receive Message

This article describes the logiBUS® exercise `Uebung_135`. We receive an ISOBUS message (PGN) and decompose the contained structured data into its individual fields.

----

## Goal of the Exercise

Track a control function (CF) joining the ISOBUS network, subscribe to its associated receive PGN, and decompose the received raw data (`8B`, 8 bytes) into individual fields.

-----

## Description and Components

![Uebung_135_network](./Uebung_135_network.svg)

- **`NmGetCfInfo_1`**: `isobus::pgn::NmGetCfInfo`, tracks a control function joining the network (`address = PRIM_TECU_ADD`, `mask = PRIM_TECU_FLT`) and supplies its name, identification, and network-event structures.
- **`AlPgnRxNew8B`**: `isobus::pgn::rx::AlPgnRxNew8B`, subscribes to a specific PGN (`u32Pgn = PGN_ELECTRONIC_STEERING_CONTROL`) for the detected control function and receives its 8-byte raw data.
- **`STRUCT_DEMUX`** (multiple instances): `eclipse4diac::convert::STRUCT_DEMUX`, decomposes a structure into its individual fields.

-----

## Behavior

1. `NmGetCfInfo_1` reports (`IND`) once the sought control function is detected on the network.
2. The supplied structures (name, identification, network event) are decomposed into individual fields via three `STRUCT_DEMUX` instances.
3. In parallel, `NmGetCfInfo_1.IND` installs the PGN receiver `AlPgnRxNew8B` for that control function.
4. When `AlPgnRxNew8B` receives a new message (`IND`), its 8-byte raw data is likewise decomposed into individual fields via `STRUCT_DEMUX`.
