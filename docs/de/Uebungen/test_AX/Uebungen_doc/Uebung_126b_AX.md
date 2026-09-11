# Uebung_126b_AX: Übung zu ISOBUS Send Message Cyclic (mit CB) SINUS-Funktion Plotten

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_126b_AX (Übung zu ISOBUS Send Message Cyclic (mit CB) SINUS-Funktion Plotten).

----

![Uebung_126b_AX_network](./Uebung_126b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Übung zu ISOBUS Send Message Cyclic (mit CB) SINUS-Funktion Plotten**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_126b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **NmGetCfInfo_1**: Instanz des Typs isobus::pgn::NmGetCfInfo.
  - Parameter u8CanIdx = NODE1
  - Parameter member = network
  - Parameter address = PEAK_ADD
  - Parameter mask = PEAK_FLT
- **AlPgnTxNew8Bcycl_REQ**: Instanz des Typs isobus::pgn::tx::AlPgnTxNew8Bcycl_REQ.
  - Parameter u32Pgn = PGN_PDU1_PropA
  - Parameter u16DaSize = 8
  - Parameter u8Priority = 3
  - Parameter u16DefRepRate = 500

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- DataSupply.PLUG1 -> AlPgnTxNew8Bcycl_REQ.CB

**Ereignisverbindungen:**

- NmGetCfInfo_1.IND -> AlPgnTxNew8Bcycl_REQ.install

**Datenverbindungen:**

- NmGetCfInfo_1.sNetEv -> AlPgnTxNew8Bcycl_REQ.NmDestin

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_126b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
