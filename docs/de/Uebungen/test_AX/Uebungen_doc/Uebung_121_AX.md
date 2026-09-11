# Uebung_121_AX: Übung zu ISOBUS Name

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_121_AX (Übung zu ISOBUS Name).

----

![Uebung_121_AX_network](./Uebung_121_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Übung zu ISOBUS Name**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_121_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **STRUCT_DEMUX**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_MUX**: Instanz des Typs eclipse4diac::convert::STRUCT_MUX.
- **NmSetName**: Instanz des Typs isobus::pgn::NmSetName.
- **NmSetNameField**: Instanz des Typs isobus::pgn::NmSetNameField.
- **STRUCT_MUX_1**: Instanz des Typs eclipse4diac::convert::STRUCT_MUX.
- **STRUCT_DEMUX_1**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **NmGetCfInfo**: Instanz des Typs isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = thisMember
  - Parameter address = ADD_ALL_PASS
  - Parameter mask = FLT_ALL_PASS
- **STRUCT_DEMUX_2**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **STRUCT_DEMUX_3**: Instanz des Typs eclipse4diac::convert::STRUCT_DEMUX.
- **INIT**: Instanz des Typs iec61131::booleanOperators::INIT.
- **INIT_1**: Instanz des Typs iec61131::booleanOperators::INIT.

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- STRUCT_MUX.CNF -> STRUCT_DEMUX.REQ
- NmSetName.CNF -> NmSetNameField.REQ
- STRUCT_MUX_1.CNF -> NmSetName.REQ
- NmSetNameField.CNF -> STRUCT_DEMUX_1.REQ
- NmGetCfInfo.IND -> STRUCT_DEMUX_3.REQ
- NmGetCfInfo.IND -> STRUCT_DEMUX_2.REQ
- INIT.INITO -> INIT.REQ
- INIT.CNF -> STRUCT_MUX_1.REQ
- INIT_1.INITO -> INIT_1.REQ
- INIT_1.CNF -> STRUCT_MUX.REQ

**Datenverbindungen:**

- STRUCT_MUX.OUT -> STRUCT_DEMUX.IN
- STRUCT_MUX_1.OUT -> NmSetName.psNameField
- NmSetName. -> NmSetNameField.au8IsoName
- NmSetNameField. -> STRUCT_DEMUX_1.IN
- NmGetCfInfo.sNetEv -> STRUCT_DEMUX_3.IN
- NmGetCfInfo.sCfInfo -> STRUCT_DEMUX_2.IN

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_121_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
