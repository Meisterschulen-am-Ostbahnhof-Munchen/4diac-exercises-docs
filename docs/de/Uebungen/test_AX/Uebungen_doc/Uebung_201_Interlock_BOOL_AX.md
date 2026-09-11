# Uebung_201_Interlock_BOOL_AX: Interlock basic exercise (BOOL)

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_201_Interlock_BOOL_AX (Interlock basic exercise (BOOL)).

----

![Uebung_201_Interlock_BOOL_AX_network](./Uebung_201_Interlock_BOOL_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Interlock basic exercise (BOOL)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_201_Interlock_BOOL_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalOutput_Q4**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **DigitalOutput_Q3**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalInput_I3**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **ILOCK_AX_3**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO_AX.
- **ILOCK_AX_4**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO_AX.
- **ILOCK_AX_1**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO_AX.
- **ILOCK_AX_2**: Instanz des Typs logiBUS::signalprocessing::interlock::ILOCK_IO_AX.
- **DigitalInput_I4**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- DigitalInput_I3.IN -> ILOCK_AX_3.IN
- DigitalInput_I4.IN -> ILOCK_AX_4.IN
- ILOCK_AX_4.OUT -> DigitalOutput_Q4.OUT
- ILOCK_AX_3.OUT -> DigitalOutput_Q3.OUT
- DigitalInput_I1.IN -> ILOCK_AX_1.IN
- ILOCK_AX_1.OUT -> DigitalOutput_Q1.OUT
- DigitalInput_I2.IN -> ILOCK_AX_2.IN
- ILOCK_AX_2.OUT -> DigitalOutput_Q2.OUT
- ILOCK_AX_1.ILOCK_OUT -> ILOCK_AX_2.ILOCK_IN
- ILOCK_AX_2.ILOCK_OUT -> ILOCK_AX_3.ILOCK_IN
- ILOCK_AX_3.ILOCK_OUT -> ILOCK_AX_4.ILOCK_IN

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_201_Interlock_BOOL_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
