# Uebung_089a: Beispiel für E_RF_TRIG

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_089a (Beispiel für E_RF_TRIG).

----

![Uebung_089a_network](./Uebung_089a_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Beispiel für E_RF_TRIG**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_089a.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IX.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **E_RF_TRIG**: Instanz des Typs iec61499::events::E_RF_TRIG.
- **E_T_FF_Q1**: Instanz des Typs iec61499::events::E_T_FF.
- **E_T_FF_Q2**: Instanz des Typs iec61499::events::E_T_FF.
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QX.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- DigitalInput_I1.IND -> E_RF_TRIG.EI
- E_RF_TRIG.ER -> E_T_FF_Q1.CLK
- E_RF_TRIG.EF -> E_T_FF_Q2.CLK
- E_T_FF_Q1.EO -> DigitalOutput_Q1.REQ
- E_T_FF_Q2.EO -> DigitalOutput_Q2.REQ

**Datenverbindungen:**

- DigitalInput_I1.IN -> E_RF_TRIG.QI
- E_T_FF_Q1.Q -> DigitalOutput_Q1.OUT
- E_T_FF_Q2.Q -> DigitalOutput_Q2.OUT

### Hinweise aus dem Modell

> Ein einziger Eingang, EIN Signal - E_RF_TRIG liefert beide Flanken gleichzeitig (ER=steigend, EF=fallend). Q1 togglet bei steigender, Q2 bei fallender Flanke.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_089a demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
