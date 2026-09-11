# Uebung_080b_AX: Beispiel für E_CTU mit AX_SPLIT_2 für Event-Verdoppelung und Terminal-Ausgabe

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_080b_AX (Beispiel für E_CTU mit AX_SPLIT_2 für Event-Verdoppelung und Terminal-Ausgabe).

----

![Uebung_080b_AX_network](./Uebung_080b_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Beispiel für E_CTU mit AX_SPLIT_2 für Event-Verdoppelung und Terminal-Ausgabe**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_080b_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **AUI_CTU**: Instanz des Typs adapter::events::unidirectional::AUI_CTU.
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_SPLIT**: Instanz des Typs iec61499::events::E_SPLIT.
- **AUI_TO_AUDI**: Instanz des Typs adapter::conversion::unidirectional::AUI_TO_AUDI.
- **Q_NumericValue_AUDI**: Instanz des Typs isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = OutputNumber_N1

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- AUI_CTU.Q -> DigitalOutput_Q1.OUT
- AUI_CTU.CV -> AUI_TO_AUDI.AUI_IN
- AUI_TO_AUDI.AUDI_OUT -> Q_NumericValue_AUDI.u32NewValue

**Ereignisverbindungen:**

- DigitalInput_CLK_I1.IND -> E_SPLIT.EI
- E_SPLIT.EO1 -> AUI_CTU.CU
- E_SPLIT.EO2 -> AUI_CTU.CU
- DigitalInput_CLK_I2.IND -> AUI_CTU.R

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_080b_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
