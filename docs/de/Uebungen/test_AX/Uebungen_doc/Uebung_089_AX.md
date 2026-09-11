# Uebung_089_AX: Beispiel für E_R_TRIG, mit Plug and Socket

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_089_AX (Beispiel für E_R_TRIG, mit Plug and Socket).

----

![Uebung_089_AX_network](./Uebung_089_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Beispiel für E_R_TRIG, mit Plug and Socket**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_089_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **AX_OR_2**: Instanz des Typs adapter::booleanOperators::AX_OR_2.
- **SPLIT_1**: Instanz des Typs adapter::events::unidirectional::AX_SPLIT_2.
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **AX_R_TRIG**: Instanz des Typs adapter::events::unidirectional::AX_R_TRIG.
- **AX_T_FF_Q1**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **AX_T_FF_1_Q2**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **AX_E_SWITCH**: Instanz des Typs adapter::events::unidirectional::AX_E_SWITCH.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- DigitalInput_I1.IN -> AX_OR_2.IN1
- DigitalInput_I2.IN -> AX_OR_2.IN2
- AX_T_FF_Q1.Q -> DigitalOutput_Q1.OUT
- AX_T_FF_1_Q2.Q -> DigitalOutput_Q2.OUT
- AX_OR_2.OUT -> SPLIT_1.IN
- SPLIT_1.OUT1 -> AX_R_TRIG.QI
- SPLIT_1.OUT2 -> AX_E_SWITCH.G

**Ereignisverbindungen:**
- AX_R_TRIG.EO -> AX_T_FF_Q1.CLK
- AX_E_SWITCH.EO1 -> AX_T_FF_1_Q2.CLK

### Hinweise aus dem Modell

> R_TRIG schaltet nur wenn wirklich steigende Flanke
> E_SWITCH schaltet auch, wenn anderweitig ein Event kommt.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_089_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
