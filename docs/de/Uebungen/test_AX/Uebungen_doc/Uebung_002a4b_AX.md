# Uebung_002a4b_AX: DigitalInput_I1/_I2 mit AND_BOOL und Negate auf DigitalOutput_Q1

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_002a4b_AX (DigitalInput_I1/_I2 mit AND_BOOL und Negate auf DigitalOutput_Q1).

----

![Uebung_002a4b_AX_network](./Uebung_002a4b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **DigitalInput_I1/_I2 mit AND_BOOL und Negate auf DigitalOutput_Q1**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_002a4b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **AX_X_TO_BOOL_1**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_2**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AND_BOOL_2**: Instanz des Typs iec61131::booleanOperators::AND_BOOL_2.
- **AX_BOOL_TO_X**: Instanz des Typs adapter::conversion::unidirectional::AX_BOOL_TO_X.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- DigitalInput_I1.IN -> AX_X_TO_BOOL_1.AX_IN
- DigitalInput_I2.IN -> AX_X_TO_BOOL_2.AX_IN
- AX_BOOL_TO_X.AX_OUT -> DigitalOutput_Q1.OUT

**Ereignisverbindungen:**

- AX_X_TO_BOOL_1.CNF -> AND_BOOL_2.REQ
- AX_X_TO_BOOL_2.CNF -> AND_BOOL_2.REQ
- AND_BOOL_2.CNF -> AX_BOOL_TO_X.REQ

**Datenverbindungen:**

- AX_X_TO_BOOL_1.IN -> AND_BOOL_2.IN1
- AX_X_TO_BOOL_2.IN -> AND_BOOL_2.IN2
- AND_BOOL_2.OUT -> AX_BOOL_TO_X.OUT

### Hinweise aus dem Modell

> "Negate Connection" mach ein NOT am Eingang; das geht nur bei BOOL.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_002a4b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
