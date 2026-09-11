# Uebung_007d_AX: Blinker mit E_CYCLE und E_T_FF

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_007d_AX (Blinker mit E_CYCLE und E_T_FF).

----

![Uebung_007d_AX_network](./Uebung_007d_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Blinker mit E_CYCLE und E_T_FF**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_007d_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **E_CYCLE**: Instanz des Typs iec61499::events::E_CYCLE.
  - Parameter DT = T#1ms
- **E_T_FF**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **E_TMIN**: Instanz des Typs iec61499::events::E_TMIN.
  - Parameter Tmin = T#10s
- **INIT**: Instanz des Typs iec61131::booleanOperators::INIT.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- E_T_FF.Q -> DigitalOutput_Q1.OUT

**Ereignisverbindungen:**

- E_CYCLE.EO -> E_TMIN.EI
- E_TMIN.EO -> E_T_FF.CLK
- INIT.INITO -> INIT.REQ
- INIT.CNF -> E_CYCLE.START

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_007d_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
