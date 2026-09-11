# Uebung_229: 2 Taster (I1/I2) auf 1 SR-Latch (Last-Wins) via 2x AX_RF_TRIG + AX_SR, Ausgang Q1

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_229 (2 Taster (I1/I2) auf 1 SR-Latch (Last-Wins) via 2x AX_RF_TRIG + AX_SR, Ausgang Q1).

----

![Uebung_229_network](./Uebung_229_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **2 Taster (I1/I2) auf 1 SR-Latch (Last-Wins) via 2x AX_RF_TRIG + AX_SR, Ausgang Q1**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_229.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **AX_RF_TRIG_1**: Instanz des Typs iec61499::events::E_RF_TRIG.
- **AX_RF_TRIG_2**: Instanz des Typs iec61499::events::E_RF_TRIG.
- **AX_SR**: Instanz des Typs iec61499::events::E_SR.
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- AX_RF_TRIG_1.ER -> AX_SR.S
- AX_RF_TRIG_2.ER -> AX_SR.S
- AX_RF_TRIG_1.EF -> AX_SR.R
- AX_RF_TRIG_2.EF -> AX_SR.R
- DigitalInput_I2.IND -> AX_RF_TRIG_2.EI
- DigitalInput_I1.IND -> AX_RF_TRIG_1.EI
- AX_SR.EO -> DigitalOutput_Q1.REQ

**Datenverbindungen:**

- DigitalInput_I2.IN -> AX_RF_TRIG_2.QI
- DigitalInput_I1.IN -> AX_RF_TRIG_1.QI
- AX_SR.Q -> DigitalOutput_Q1.OUT

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_229 demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
