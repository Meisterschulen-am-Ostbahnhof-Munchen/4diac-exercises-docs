# Uebung_055_AX: DigitalInput_I1 auf DigitalOutput_Q1, mit Plug and Socket

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_055_AX (DigitalInput_I1 auf DigitalOutput_Q1, mit Plug and Socket).

----

![Uebung_055_AX_network](./Uebung_055_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **DigitalInput_I1 auf DigitalOutput_Q1, mit Plug and Socket**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_055_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **Q_TO_STR_STATUS**: Instanz des Typs logiBUS::utils::quarter::QUARTER_TO_STR_STATUS.
- **Q_TO_BOOL**: Instanz des Typs logiBUS::utils::quarter::QUARTER_TO_BOOL.
- **BOOL_TO_Q**: Instanz des Typs logiBUS::utils::quarter::BOOL_TO_QUARTER.
- **AX_X_TO_BOOL**: Instanz des Typs adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **AX_BOOL_TO_X**: Instanz des Typs adapter::conversion::unidirectional::AX_BOOL_TO_X.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- DigitalInput_I1.IN -> AX_X_TO_BOOL.AX_IN
- AX_BOOL_TO_X.AX_OUT -> DigitalOutput_Q1.OUT

**Ereignisverbindungen:**

- Q_TO_BOOL.CNF -> AX_BOOL_TO_X.REQ
- AX_X_TO_BOOL.CNF -> BOOL_TO_Q.REQ
- BOOL_TO_Q.CNF -> Q_TO_STR_STATUS.REQ
- BOOL_TO_Q.CNF -> Q_TO_BOOL.REQ

**Datenverbindungen:**

- Q_TO_BOOL.Q -> AX_BOOL_TO_X.OUT
- AX_X_TO_BOOL.IN -> BOOL_TO_Q.I
- BOOL_TO_Q. -> Q_TO_STR_STATUS.IB
- BOOL_TO_Q. -> Q_TO_BOOL.IB

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_055_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
