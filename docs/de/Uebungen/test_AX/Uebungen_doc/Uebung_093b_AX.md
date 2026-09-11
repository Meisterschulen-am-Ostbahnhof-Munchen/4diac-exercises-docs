# Uebung_093b_AX: Beispiel für E_N_TABLE

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_093b_AX (Beispiel für E_N_TABLE).

----

![Uebung_093b_AX_network](./Uebung_093b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Beispiel für E_N_TABLE**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_093b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **E_N_TABLE**: Instanz des Typs iec61499::events::E_N_TABLE.
  - Parameter DT = [T#0s, T#2s, T#3s, T#4s]
  - Parameter N = 4
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_T_FF_Q2**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **E_T_FF_Q4**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **E_T_FF_Q3**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q3**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q3
- **E_T_FF_Q1**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2
- **DigitalOutput_Q4**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q4

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- E_T_FF_Q1.Q -> DigitalOutput_Q1.OUT
- E_T_FF_Q4.Q -> DigitalOutput_Q4.OUT
- E_T_FF_Q2.Q -> DigitalOutput_Q2.OUT
- E_T_FF_Q3.Q -> DigitalOutput_Q3.OUT

**Ereignisverbindungen:**
- DigitalInput_CLK_I1.IND -> E_N_TABLE.START
- E_N_TABLE.EO0 -> E_T_FF_Q1.CLK
- E_N_TABLE.EO1 -> E_T_FF_Q2.CLK
- E_N_TABLE.EO2 -> E_T_FF_Q3.CLK
- E_N_TABLE.EO3 -> E_T_FF_Q4.CLK

### Hinweise aus dem Modell

> E_TABLE wird 4 Events ausgeben, das erste sofort nach dem Click, das letzte 9s nach dem Click
[T#0s, T#2s, T#3s, T#4s]

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_093b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
