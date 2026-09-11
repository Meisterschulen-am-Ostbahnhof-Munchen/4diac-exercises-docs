# Uebung_006c_AX: SR-Flip-Flop mit IB auf DI_REPEAT

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_006c_AX (SR-Flip-Flop mit IB auf DI_REPEAT).

----

![Uebung_006c_AX_network](./Uebung_006c_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **SR-Flip-Flop mit IB auf DI_REPEAT**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_006c_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_RPT_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IBA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_PRESS_REPEAT
- **DigitalInput_RPT_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IBA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_PRESS_REPEAT
- **E_SR_Q1**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **E_SR_Q2**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalOutput_Q3**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **DigitalOutput_Q4**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4
- **E_SR_Q5**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **E_SR_Q6**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **DigitalOutput_Q5**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q5
- **DigitalOutput_Q6**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q6
- **DigitalOutput_Q7**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q7
- **DigitalOutput_Q8**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q8
- **AUI_DEMUX8_S**: Instanz des Typs adapter::events::unidirectional::AUI_DEMUX_8.
- **AUI_DEMUX8_R**: Instanz des Typs adapter::events::unidirectional::AUI_DEMUX_8.
- **E_SR_Q3**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **E_SR_Q4**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **E_SR_Q7**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **E_SR_Q8**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **AB_TO_AUI_S**: Instanz des Typs adapter::conversion::unidirectional::AB_TO_AUI.
- **AB_TO_AUI_R**: Instanz des Typs adapter::conversion::unidirectional::AB_TO_AUI.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- DigitalInput_RPT_I1.IN -> AB_TO_AUI_S.AB_IN
- AB_TO_AUI_S.AUI_OUT -> AUI_DEMUX8_S.K
- DigitalInput_RPT_I2.IN -> AB_TO_AUI_R.AB_IN
- AB_TO_AUI_R.AUI_OUT -> AUI_DEMUX8_R.K
- E_SR_Q1.Q -> DigitalOutput_Q1.OUT
- E_SR_Q2.Q -> DigitalOutput_Q2.OUT
- E_SR_Q3.Q -> DigitalOutput_Q3.OUT
- E_SR_Q4.Q -> DigitalOutput_Q4.OUT
- E_SR_Q5.Q -> DigitalOutput_Q5.OUT
- E_SR_Q6.Q -> DigitalOutput_Q6.OUT
- E_SR_Q7.Q -> DigitalOutput_Q7.OUT
- E_SR_Q8.Q -> DigitalOutput_Q8.OUT

**Ereignisverbindungen:**
- AUI_DEMUX8_S.EO1 -> E_SR_Q1.S
- AUI_DEMUX8_S.EO2 -> E_SR_Q2.S
- AUI_DEMUX8_S.EO3 -> E_SR_Q3.S
- AUI_DEMUX8_S.EO4 -> E_SR_Q4.S
- AUI_DEMUX8_S.EO5 -> E_SR_Q5.S
- AUI_DEMUX8_S.EO6 -> E_SR_Q6.S
- AUI_DEMUX8_S.EO7 -> E_SR_Q7.S
- AUI_DEMUX8_S.EO8 -> E_SR_Q8.S
- AUI_DEMUX8_R.EO1 -> E_SR_Q1.R
- AUI_DEMUX8_R.EO2 -> E_SR_Q2.R
- AUI_DEMUX8_R.EO3 -> E_SR_Q3.R
- AUI_DEMUX8_R.EO4 -> E_SR_Q4.R
- AUI_DEMUX8_R.EO5 -> E_SR_Q5.R
- AUI_DEMUX8_R.EO6 -> E_SR_Q6.R
- AUI_DEMUX8_R.EO7 -> E_SR_Q7.R
- AUI_DEMUX8_R.EO8 -> E_SR_Q8.R

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_006c_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
