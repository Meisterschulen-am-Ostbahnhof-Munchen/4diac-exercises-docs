# Uebung_172_AX: Exercise for E_MUX_2

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_172_AX (Exercise for E_MUX_2).

----

![Uebung_172_AX_network](./Uebung_172_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Exercise for E_MUX_2**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_172_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **E_MUX_2_1**: Instanz des Typs iec61499::events::E_MUX_2.
- **INIT**: Instanz des Typs iec61131::booleanOperators::INIT.

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**
- INIT.INITO -> INIT.REQ
- INIT.CNF -> E_MUX_2_1.EI1

### Hinweise aus dem Modell

> TODO

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_172_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
