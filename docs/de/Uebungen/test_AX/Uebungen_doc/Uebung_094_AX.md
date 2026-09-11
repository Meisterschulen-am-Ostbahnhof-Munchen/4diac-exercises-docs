# Uebung_094_AX: Beispiel für E_PERMIT

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_094_AX (Beispiel für E_PERMIT).

----

![Uebung_094_AX_network](./Uebung_094_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Beispiel für E_PERMIT**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_094_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_PERMIT**: Instanz des Typs adapter::events::unidirectional::AX_E_PERMIT.
- **E_T_FF**: Instanz des Typs adapter::events::unidirectional::AX_T_FF.
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- DigitalInput_I1.IN -> E_PERMIT.PERMIT
- E_T_FF.Q -> DigitalOutput_Q1.OUT

**Ereignisverbindungen:**
- DigitalInput_CLK_I2.IND -> E_PERMIT.EI
- E_PERMIT.EO -> E_T_FF.CLK

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_094_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
