# Uebung_004b2b_AX: Zwei unabhängige Toggle-Flip-Flops unter Verwendung von Sub-Applikationen

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_004b2b_AX (Zwei unabhängige Toggle-Flip-Flops unter Verwendung von Sub-Applikationen).

----

![Uebung_004b2b_AX_network](./Uebung_004b2b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Zwei unabhängige Toggle-Flip-Flops unter Verwendung von Sub-Applikationen**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_004b2b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- Uebung_004b2b_sub1.Q -> DigitalOutput_Q1.OUT
- Uebung_004b2b_sub2.Q -> DigitalOutput_Q2.OUT

**Ereignisverbindungen:**
- DigitalInput_CLK_I1.IND -> Uebung_004b2b_sub1.IND
- DigitalInput_CLK_I2.IND -> Uebung_004b2b_sub2.IND

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_004b2b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
