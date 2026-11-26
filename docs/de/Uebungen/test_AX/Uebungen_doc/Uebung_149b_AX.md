# Uebung_149b_AX: Übung zu ETP empfangen (PGN 0x1EF00, 4000 Byte), Anzeige im Watch

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_149b_AX (Übung zu ETP empfangen (PGN 0x1EF00, 4000 Byte), Anzeige im Watch).

----

![Uebung_149b_AX_network](./Uebung_149b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Übung zu ETP empfangen (PGN 0x1EF00, 4000 Byte), Anzeige im Watch**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_149b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **STRUCT_DEMUX_3**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo_1**: Instanz des Typs isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = network
  - Parameter address = LOGIBUS_ADD
  - Parameter mask = LOGIBUS_FLT
- **STRUCT_DEMUX_4**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_5**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **INIT_ARR_4000_BYTE**: Instanz des Typs eclipse4diac::convert::providers::PROVIDE_ARR_4000_BYTE.
- **AlPgnRxNew_TP**: Instanz des Typs isobus::pgn::rx::AlPgnRxNew_TP.
  - Parameter u32Pgn = PGN_PDU1_PropA2
  - Parameter u8Priority = 3
- **F_BYTES_TO_STRING**: Instanz des Typs logiBUS::utils::dyn_arr::F_BYTES_TO_STRING.
- **NetEv_Install**: Untergeordnete Subapplikation (SubApp) MyLib::sys::NetEv_Install_Filter.

Die Übung nutzt das gemeinsame Muster mit `NmGetCfInfo` (IND/RSP): `IND` löst `STRUCT_DEMUX` aus, `NetEv_Install` verarbeitet das Ereignis und quittiert mit `RSP`.

-----

## Ablauf und erwartetes Verhalten (aus dem Kommentar im Netzwerk)

- ETP empfangen: PGN PropA2 0x1EF00, 4000 Byte vom anderen logiBUS (Übung 149 oder 149d)
- Mehr als 1785 Byte gehen per ETP (Extended Transport Protocol), nur an einen Empfänger.
- Partner: NmGetCfInfo mit network + Filter LOGIBUS_ADD/LOGIBUS_FLT. Puffer 4000 Byte.
- Watch: u16DaSize = 4000, OVERFLOW = FALSE, Text = ETP-Test 4000 Byte.
- Firmware ab LOGIBUS 24534469 auf BEIDEN Geräten.

Hinweis: Die Übung ist noch nicht auf Hardware getestet. Die Beschreibung stammt aus der Subapplikation und ihrem Kommentar, das Verhalten ist als erwartet zu lesen.

-----

## Zusammenfassung

Die Übung Uebung_149b_AX demonstriert die modulare IEC 61499 Architektur in 4diac IDE.
