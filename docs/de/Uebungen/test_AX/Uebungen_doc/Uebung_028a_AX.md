# Uebung_028a_AX: Analog-Eingang Kalibrierung mit Adaptern INI

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_028a_AX (Analog-Eingang Kalibrierung mit Adaptern INI).

----

![Uebung_028a_AX_network](./Uebung_028a_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Analog-Eingang Kalibrierung mit Adaptern INI**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_028a_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **AnalogInput_I4**: Instanz des Typs logiBUS::io::AI::logiBUS_AI_IDA.
  - Parameter QI = TRUE
  - Parameter Input = AnalogInput_I4
  - Parameter AnalogInput_hysteresis = 50
  - Parameter TimeDelta = 250
  - Parameter TimeRateLimit = 100
- **CALIBRATE**: Instanz des Typs adapter::Engineering::measurements::AR_CALIBRATE.
  - Parameter Y_Offset = 100.0
  - Parameter Y_Scale = 600.0
- **INI_OFFSET**: Instanz des Typs eclipse4diac::storage::INI_AR2.
  - Parameter QI = TRUE
  - Parameter SETM = TRUE
  - Parameter SECTION = 'Uebung_028a_AX'
  - Parameter KEY = 'OFFSET'
  - Parameter DEFAULT_VALUE = 0.0
- **INI_SCALE**: Instanz des Typs eclipse4diac::storage::INI_AR2.
  - Parameter QI = TRUE
  - Parameter SETM = TRUE
  - Parameter SECTION = 'Uebung_028a_AX'
  - Parameter KEY = 'SCALE'
  - Parameter DEFAULT_VALUE = 1.0
- **DigitalInput_I2_CO**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **DigitalInput_I3_CS**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **AX_SPLIT_2**: Instanz des Typs adapter::events::unidirectional::AX_SPLIT_2.
- **AD_TO_AUDI**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.
- **AUDI_TO_AR**: Instanz des Typs adapter::conversion::unidirectional::AUDI_TO_AR.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- DigitalInput_I1.IN -> AX_SPLIT_2.IN
- AnalogInput_I4.IN -> AD_TO_AUDI.AD_IN
- AUDI_TO_AR.AR_OUT -> CALIBRATE.X
- DigitalInput_I2_CO.IN -> CALIBRATE.CO
- DigitalInput_I3_CS.IN -> CALIBRATE.CS
- AX_SPLIT_2.OUT1 -> DigitalOutput_Q1.OUT
- AX_SPLIT_2.OUT2 -> AnalogInput_I4.SREQ
- CALIBRATE.OFFSET -> INI_OFFSET.VAL
- CALIBRATE.SCALE -> INI_SCALE.VAL
- AD_TO_AUDI.AUDI_OUT -> AUDI_TO_AR.AUDI_IN

### Hinweise aus dem Modell

> WICHTIG ! Doppelte Konvertierung. ein AD_TO_AR wäre wie ein "reinterpret_cast"

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_028a_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
