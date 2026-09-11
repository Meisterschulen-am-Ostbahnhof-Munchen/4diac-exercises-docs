# Uebung_004a10a: Toggle Flip-Flop mit IE mit BUTTON_SINGLE_CLICK und INIT auf FALSE

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_004a10a (Toggle Flip-Flop mit IE mit BUTTON_SINGLE_CLICK und INIT auf FALSE).

----

![Uebung_004a10a_network](./Uebung_004a10a_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Toggle Flip-Flop mit IE mit BUTTON_SINGLE_CLICK und INIT auf FALSE**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_004a10a.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AX_T_FF_INIT**: Instanz des Typs iec61499::events::E_T_FF_INIT.
  - Parameter QI = TRUE
  - Parameter Q_INIT = FALSE

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- DigitalInput_CLK_I1.IND -> AX_T_FF_INIT.CLK
- AX_T_FF_INIT.EO -> DigitalOutput_Q1.REQ

**Datenverbindungen:**

- AX_T_FF_INIT.Q -> DigitalOutput_Q1.OUT

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_004a10a demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
