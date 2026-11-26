# Uebung_149c: Übung zu ETP empfangen mit zu kleinem Puffer (OVERFLOW)

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_149c (Übung zu ETP empfangen mit zu kleinem Puffer (OVERFLOW)).

----

![Uebung_149c_network](./Uebung_149c_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Übung zu ETP empfangen mit zu kleinem Puffer (OVERFLOW)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_149c.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **STRUCT_DEMUX_3**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo_1**: Instanz des Typs isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = network
  - Parameter address = LOGIBUS_ADD
  - Parameter mask = LOGIBUS_FLT
- **STRUCT_DEMUX_4**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_5**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **INIT_ARR_1785_BYTE**: Instanz des Typs eclipse4diac::convert::providers::PROVIDE_ARR_1785_BYTE.
- **AlPgnRxNew_TP**: Instanz des Typs isobus::pgn::rx::AlPgnRxNew_TP.
  - Parameter u32Pgn = PGN_PDU1_PropA2
  - Parameter u8Priority = 3
- **F_BYTES_TO_STRING**: Instanz des Typs logiBUS::utils::dyn_arr::F_BYTES_TO_STRING.
- **NetEv_Install**: Untergeordnete Subapplikation (SubApp) MyLib::sys::NetEv_Install_Filter.

Die Übung nutzt das gemeinsame Muster mit `NmGetCfInfo` (IND/RSP): `IND` löst `STRUCT_DEMUX` aus, `NetEv_Install` verarbeitet das Ereignis und quittiert mit `RSP`.

-----

## Ablauf und erwartetes Verhalten (aus dem Kommentar im Netzwerk)

- ETP empfangen mit zu kleinem Puffer: PGN PropA2 0x1EF00 (Sender: Übung 149)
- Der Partner sendet 4000 Byte, der Puffer hat nur 1785 Byte.
- Watch: u16DaSize = 4000 (echte Länge), OVERFLOW = TRUE, Data ist nach 1785 Byte abgeschnitten.
- Firmware ab LOGIBUS 24534469 auf BEIDEN Geräten.

Hinweis: Die Übung ist noch nicht auf Hardware getestet. Die Beschreibung stammt aus der Subapplikation und ihrem Kommentar, das Verhalten ist als erwartet zu lesen.

-----

## Zusammenfassung

Die Übung Uebung_149c demonstriert die modulare IEC 61499 Architektur in 4diac IDE.
