# Uebung_004b2_AX: Toggle Flip-Flop mit IE / Split / doppelt (aus AX_E_SWITCH und AX_SR aufgebaut)

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_004b2_AX (Toggle Flip-Flop mit IE / Split / doppelt (aus AX_E_SWITCH und AX_SR aufgebaut)).

----

![Uebung_004b2_AX_network](./Uebung_004b2_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Toggle Flip-Flop mit IE / Split / doppelt (aus AX_E_SWITCH und AX_SR aufgebaut)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_004b2_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_SWITCH_I1**: Instanz des Typs adapter::events::unidirectional::AX_E_SWITCH.
- **AX_SR_I1**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_SWITCH_I2**: Instanz des Typs adapter::events::unidirectional::AX_E_SWITCH.
- **AX_SR_I2**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **SPLIT_1**: Instanz des Typs adapter::events::unidirectional::AX_SPLIT_2.
- **SPLIT_2**: Instanz des Typs adapter::events::unidirectional::AX_SPLIT_2.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- AX_SR_I1.Q -> SPLIT_1.IN
- SPLIT_1.OUT2 -> E_SWITCH_I1.G
- SPLIT_2.OUT2 -> E_SWITCH_I2.G
- AX_SR_I2.Q -> SPLIT_2.IN
- SPLIT_2.OUT1 -> DigitalOutput_Q2.OUT
- SPLIT_1.OUT1 -> DigitalOutput_Q1.OUT

**Ereignisverbindungen:**

- DigitalInput_CLK_I1.IND -> E_SWITCH_I1.EI
- E_SWITCH_I1.EO0 -> AX_SR_I1.S
- E_SWITCH_I1.EO1 -> AX_SR_I1.R
- DigitalInput_CLK_I2.IND -> E_SWITCH_I2.EI
- E_SWITCH_I2.EO0 -> AX_SR_I2.S
- E_SWITCH_I2.EO1 -> AX_SR_I2.R

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_004b2_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
