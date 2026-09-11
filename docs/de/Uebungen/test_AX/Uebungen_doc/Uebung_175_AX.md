# Uebung_175_AX: Exercise for E_TABLE_CTRL

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_175_AX (Exercise for E_TABLE_CTRL).

----

![Uebung_175_AX_network](./Uebung_175_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Exercise for E_TABLE_CTRL**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_175_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **E_TABLE_CTRL_1**: Instanz des Typs iec61499::events::E_TABLE_CTRL.
- **INIT**: Instanz des Typs iec61131::booleanOperators::INIT.

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**
- INIT.INITO -> INIT.REQ
- INIT.CNF -> E_TABLE_CTRL_1.CLK

### Hinweise aus dem Modell

> TODO

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_175_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
