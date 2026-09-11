# Uebung_007b_AX: Blinker mit E_CYCLE und E_T_FF

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_007b_AX (Blinker mit E_CYCLE und E_T_FF).

----

![Uebung_007b_AX_network](./Uebung_007b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Blinker mit E_CYCLE und E_T_FF**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_007b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **E_CYCLE**: Instanz des Typs iec61499::events::E_CYCLE.
  - Parameter DT = T#10ms
- **E_T_FF**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **E_SPLIT_4**: Instanz des Typs iec61499::events::E_SPLIT_4.
- **E_MERGE_4**: Instanz des Typs iec61499::events::E_MERGE_4.
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- E_T_FF.Q -> DigitalOutput_Q1.OUT

**Ereignisverbindungen:**

- E_MERGE_4.EO -> E_T_FF.CLK
- E_SPLIT_4.EO1 -> E_MERGE_4.EI1
- E_SPLIT_4.EO2 -> E_MERGE_4.EI2
- E_SPLIT_4.EO3 -> E_MERGE_4.EI3
- E_SPLIT_4.EO4 -> E_MERGE_4.EI4
- E_CYCLE.EO -> E_SPLIT_4.EI
- DigitalInput_CLK_I1.IND -> E_CYCLE.START
- DigitalInput_CLK_I2.IND -> E_CYCLE.STOP

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_007b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
