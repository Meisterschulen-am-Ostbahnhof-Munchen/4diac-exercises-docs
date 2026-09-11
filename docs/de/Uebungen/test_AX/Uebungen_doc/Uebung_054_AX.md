# Uebung_054_AX: DigitalInput_I1-_I4 auf DigitalOutput_Q1-_Q4, mit Plug and Socket

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_054_AX (DigitalInput_I1-_I4 auf DigitalOutput_Q1-_Q4, mit Plug and Socket).

----

![Uebung_054_AX_network](./Uebung_054_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **DigitalInput_I1-_I4 auf DigitalOutput_Q1-_Q4, mit Plug and Socket**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_054_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **DigitalOutput_Q3**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalInput_I3**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **DigitalOutput_Q4**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **DigitalInput_I4**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
- **ARR08B_TO_BOOLS**: Instanz des Typs logiBUS::utils::conversion::arr::forwarding::ARR08X_TO_BOOLS.
- **BOOLS_TO_ARR08B**: Instanz des Typs logiBUS::utils::conversion::arr::reversing::BOOLS_TO_ARR08X.
- **AX_X_TO_BOOL_1**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_2**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_3**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_X_TO_BOOL_4**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_BOOL_TO_X_1**: Instanz des Typs adapter::conversion::unidirectional::AX_BOOL_TO_X.
- **AX_BOOL_TO_X_2**: Instanz des Typs adapter::conversion::unidirectional::AX_BOOL_TO_X.
- **AX_BOOL_TO_X_3**: Instanz des Typs adapter::conversion::unidirectional::AX_BOOL_TO_X.
- **AX_BOOL_TO_X_4**: Instanz des Typs adapter::conversion::unidirectional::AX_BOOL_TO_X.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- DigitalInput_I1.IN -> AX_X_TO_BOOL_1.AX_IN
- DigitalInput_I2.IN -> AX_X_TO_BOOL_2.AX_IN
- DigitalInput_I3.IN -> AX_X_TO_BOOL_3.AX_IN
- DigitalInput_I4.IN -> AX_X_TO_BOOL_4.AX_IN
- AX_BOOL_TO_X_1.AX_OUT -> DigitalOutput_Q1.OUT
- AX_BOOL_TO_X_2.AX_OUT -> DigitalOutput_Q2.OUT
- AX_BOOL_TO_X_3.AX_OUT -> DigitalOutput_Q3.OUT
- AX_BOOL_TO_X_4.AX_OUT -> DigitalOutput_Q4.OUT

**Ereignisverbindungen:**
- BOOLS_TO_ARR08B.CNF -> ARR08B_TO_BOOLS.REQ
- AX_X_TO_BOOL_1.CNF -> BOOLS_TO_ARR08B.REQ
- AX_X_TO_BOOL_2.CNF -> BOOLS_TO_ARR08B.REQ
- AX_X_TO_BOOL_3.CNF -> BOOLS_TO_ARR08B.REQ
- AX_X_TO_BOOL_4.CNF -> BOOLS_TO_ARR08B.REQ
- ARR08B_TO_BOOLS.CNF -> AX_BOOL_TO_X_1.REQ
- ARR08B_TO_BOOLS.CNF -> AX_BOOL_TO_X_2.REQ
- ARR08B_TO_BOOLS.CNF -> AX_BOOL_TO_X_3.REQ
- ARR08B_TO_BOOLS.CNF -> AX_BOOL_TO_X_4.REQ

**Datenverbindungen:**
- BOOLS_TO_ARR08B.OUT -> ARR08B_TO_BOOLS.IN
- AX_X_TO_BOOL_1.IN -> BOOLS_TO_ARR08B.IN_00
- AX_X_TO_BOOL_2.IN -> BOOLS_TO_ARR08B.IN_01
- AX_X_TO_BOOL_3.IN -> BOOLS_TO_ARR08B.IN_02
- AX_X_TO_BOOL_4.IN -> BOOLS_TO_ARR08B.IN_03
- ARR08B_TO_BOOLS.OUT_00 -> AX_BOOL_TO_X_1.OUT
- ARR08B_TO_BOOLS.OUT_01 -> AX_BOOL_TO_X_2.OUT
- ARR08B_TO_BOOLS.OUT_02 -> AX_BOOL_TO_X_3.OUT
- ARR08B_TO_BOOLS.OUT_03 -> AX_BOOL_TO_X_4.OUT

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_054_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
