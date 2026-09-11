# Uebung_006a5: SR und T-Flip-Flop als Rastend/Tastend Implementierung

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_006a5 (SR und T-Flip-Flop als Rastend/Tastend Implementierung).

----

![Uebung_006a5_network](./Uebung_006a5_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **SR und T-Flip-Flop als Rastend/Tastend Implementierung**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_006a5.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **BUTTON_LONG_PRESS_START**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_LONG_PRESS_START
- **BUTTON_LONG_PRESS_UP**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_LONG_PRESS_UP
- **E_T_FF_SR**: Instanz des Typs iec61499::events::E_T_FF_SR.
- **BUTTON_SINGLE_CLICK**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter PARAMS = 
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- BUTTON_LONG_PRESS_START.IND -> E_T_FF_SR.S
- BUTTON_LONG_PRESS_UP.IND -> E_T_FF_SR.R
- BUTTON_SINGLE_CLICK.IND -> E_T_FF_SR.CLK
- E_T_FF_SR.EO -> DigitalOutput_Q1.REQ

**Datenverbindungen:**

- E_T_FF_SR.Q -> DigitalOutput_Q1.OUT

### Hinweise aus dem Modell

> Universal Eingang: 
so können wir mit einem Taster ODER  einem Schalter arbeiten.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_006a5 demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
