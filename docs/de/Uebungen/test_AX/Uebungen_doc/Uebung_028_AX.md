# Uebung_028_AX: Analog-Eingang

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_028_AX (Analog-Eingang).

----

![Uebung_028_AX_network](./Uebung_028_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Analog-Eingang**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_028_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I1
- **AnalogInput_I4**: Instanz des Typs logiBUS::io::AI::logiBUS_AI_IDA.
  - Parameter QI = TRUE
  - Parameter Input = logiBUS::io::AI::logiBUS_AI::AnalogInput_I4
  - Parameter AnalogInput_hysteresis = 50
  - Parameter TimeDelta = 250
  - Parameter TimeRateLimit = 100
- **AnalogInput_I7**: Instanz des Typs logiBUS::io::AI::logiBUS_AI_IDA.
  - Parameter QI = TRUE
  - Parameter Input = logiBUS::io::AI::logiBUS_AI::AnalogInput_I7
  - Parameter AnalogInput_hysteresis = 50
  - Parameter TimeDelta = 250
  - Parameter TimeRateLimit = 100
- **F_DWORD_TO_UDINT_I7**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.
- **F_DWORD_TO_UDINT_I4**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.
- **AX_X_TO_BOOL**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_SPLIT_2**: Instanz des Typs adapter::events::unidirectional::AX_SPLIT_2.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- AnalogInput_I7.IN -> F_DWORD_TO_UDINT_I7.AD_IN
- AX_SPLIT_2.OUT1 -> DigitalOutput_Q1.OUT
- AnalogInput_I4.IN -> F_DWORD_TO_UDINT_I4.AD_IN
- DigitalInput_I1.IN -> AX_SPLIT_2.IN
- AX_SPLIT_2.OUT2 -> AX_X_TO_BOOL.AX_IN

**Ereignisverbindungen:**

- AX_X_TO_BOOL.CNF -> AnalogInput_I4.REQ
- AX_X_TO_BOOL.CNF -> AnalogInput_I7.REQ

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_028_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
