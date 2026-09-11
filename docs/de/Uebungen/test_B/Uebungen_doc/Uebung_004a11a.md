# Uebung_004a11a: Toggle Flip-Flop mit IE mit BUTTON_SINGLE_CLICK und STORE (INI)

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_004a11a (Toggle Flip-Flop mit IE mit BUTTON_SINGLE_CLICK und STORE (INI)).

----

![Uebung_004a11a_network](./Uebung_004a11a_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Toggle Flip-Flop mit IE mit BUTTON_SINGLE_CLICK und STORE (INI)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_004a11a.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AX_T_FF_SR_SYM_STORE**: Instanz des Typs adapter::iec61499::events::E_T_FF_SR_SYM_STORE.
- **INI_AX2**: Instanz des Typs eclipse4diac::storage::INI_AX2.
  - Parameter QI = TRUE
  - Parameter SECTION = 'INI_AX2'
  - Parameter KEY = 'U004a11a_AX'
  - Parameter DEFAULT_VALUE = FALSE

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- AX_T_FF_SR_SYM_STORE.Q_INIT -> INI_AX2.VAL

**Ereignisverbindungen:**

- DigitalInput_CLK_I1.IND -> AX_T_FF_SR_SYM_STORE.CLK
- AX_T_FF_SR_SYM_STORE.EO -> DigitalOutput_Q1.REQ

**Datenverbindungen:**

- AX_T_FF_SR_SYM_STORE.Q -> DigitalOutput_Q1.OUT

### Hinweise aus dem Modell

> am Anfang letzten Zustand laden!

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_004a11a demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
