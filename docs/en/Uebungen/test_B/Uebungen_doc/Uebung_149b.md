# Uebung_149b: Übung zu ETP empfangen (PGN 0x1EF00, 4000 Byte), Anzeige im Watch

This article describes the 4diac IDE sub-application Uebung_149b (Übung zu ETP empfangen (PGN 0x1EF00, 4000 Byte), Anzeige im Watch).

----

![Uebung_149b_network](./Uebung_149b_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Übung zu ETP empfangen (PGN 0x1EF00, 4000 Byte), Anzeige im Watch**

-----

## Description and Components

The exercise consists of the sub-application Uebung_149b.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **STRUCT_DEMUX_3**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo_1**: Instance of type isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = network
  - Parameter address = LOGIBUS_ADD
  - Parameter mask = LOGIBUS_FLT
- **STRUCT_DEMUX_4**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_5**: Instance of type eclipse4diac::convert::STRUCT_DEMUX.
- **INIT_ARR_4000_BYTE**: Instance of type eclipse4diac::convert::providers::PROVIDE_ARR_4000_BYTE.
- **AlPgnRxNew_TP**: Instance of type isobus::pgn::rx::AlPgnRxNew_TP.
  - Parameter u32Pgn = PGN_PDU1_PropA2
  - Parameter u8Priority = 3
- **F_BYTES_TO_STRING**: Instance of type logiBUS::utils::dyn_arr::F_BYTES_TO_STRING.
- **NetEv_Install**: Nested sub-application (SubApp) MyLib::sys::NetEv_Install_Filter.

The exercise uses the common pattern with `NmGetCfInfo` (IND/RSP): `IND` triggers `STRUCT_DEMUX`, `NetEv_Install` handles the event and acknowledges with `RSP`.

-----

## Sequence and expected behavior (from the comment in the network)

- ETP empfangen: PGN PropA2 0x1EF00, 4000 Byte vom anderen logiBUS (Übung 149 oder 149d)
- Mehr als 1785 Byte gehen per ETP (Extended Transport Protocol), nur an einen Empfänger.
- Partner: NmGetCfInfo mit network + Filter LOGIBUS_ADD/LOGIBUS_FLT. Puffer 4000 Byte.
- Watch: u16DaSize = 4000, OVERFLOW = FALSE, Text = ETP-Test 4000 Byte.
- Firmware ab LOGIBUS 24534469 auf BEIDEN Geräten.

Note: The exercise has not been tested on hardware yet. The description comes from the sub-application and its comment, the behavior is to be read as expected.

-----

## Summary

Exercise Uebung_149b demonstrates the modular IEC 61499 architecture in 4diac IDE.
