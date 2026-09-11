# Uebung_201_Interlock: Interlock advanced exercise (AX)

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_201_Interlock (Interlock advanced exercise (AX)).

----

![Uebung_201_Interlock_network](./Uebung_201_Interlock_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Interlock advanced exercise (AX)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_201_Interlock.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalOutput_Q4**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **ILOCK_1**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO.
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **ILOCK_2**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO.
- **DigitalOutput_Q3**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **ILOCK_3**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO.
- **ILOCK_4**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO.
- **DigitalInput_I3**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **DigitalInput_I4**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- ILOCK_1.ILOCK_OUT -> ILOCK_2.ILOCK_IN
- ILOCK_2.ILOCK_OUT -> ILOCK_3.ILOCK_IN
- ILOCK_3.ILOCK_OUT -> ILOCK_4.ILOCK_IN

**Ereignisverbindungen:**

- DigitalInput_I4.IND -> ILOCK_4.REQ
- DigitalInput_I3.IND -> ILOCK_3.REQ
- DigitalInput_I2.IND -> ILOCK_2.REQ
- DigitalInput_I1.IND -> ILOCK_1.REQ
- ILOCK_1.CNF -> DigitalOutput_Q1.REQ
- ILOCK_2.CNF -> DigitalOutput_Q2.REQ
- ILOCK_3.CNF -> DigitalOutput_Q3.REQ
- ILOCK_4.CNF -> DigitalOutput_Q4.REQ

**Datenverbindungen:**

- DigitalInput_I4.IN -> ILOCK_4.IN
- DigitalInput_I3.IN -> ILOCK_3.IN
- DigitalInput_I2.IN -> ILOCK_2.IN
- DigitalInput_I1.IN -> ILOCK_1.IN
- ILOCK_4.OUT -> DigitalOutput_Q4.OUT
- ILOCK_3.OUT -> DigitalOutput_Q3.OUT
- ILOCK_2.OUT -> DigitalOutput_Q2.OUT
- ILOCK_1.OUT -> DigitalOutput_Q1.OUT

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_201_Interlock demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
