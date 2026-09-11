# Uebung_019b_AX: Umschalten einer Maske, mit Plug and Socket

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_019b_AX (Umschalten einer Maske, mit Plug and Socket).

----

![Uebung_019b_AX_network](./Uebung_019b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Umschalten einer Maske, mit Plug and Socket**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_019b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I3**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **ACK**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = ID_NULL
  - Parameter InputEvent = SK_PRESSED
- **Q_ActiveMask**: Instanz des Typs isobus::UT::Q::Q_ActiveMask_AUI.
- **AX_SR**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **Alarmausgang**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- Select_4_AUI.OUT -> Q_ActiveMask.u16NewMaskId
- AX_SR.Q -> Alarmausgang.OUT

**Ereignisverbindungen:**

- DigitalInput_CLK_I1.IND -> Select_4_AUI.EI1
- DigitalInput_CLK_I2.IND -> Select_4_AUI.EI2
- DigitalInput_CLK_I3.IND -> Select_4_AUI.EI3
- ACK.IND -> Select_4_AUI.EI4
- DigitalInput_CLK_I3.IND -> AX_SR.S
- DigitalInput_CLK_I2.IND -> AX_SR.R
- DigitalInput_CLK_I1.IND -> AX_SR.R
- ACK.IND -> AX_SR.R

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_019b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
