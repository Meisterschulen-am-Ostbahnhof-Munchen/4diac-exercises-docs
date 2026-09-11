# Uebung_020f4: DigitalInput_I1 auf DataPanel_1A; BLINKER

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_020f4 (DigitalInput_I1 auf DataPanel_1A; BLINKER).

----

![Uebung_020f4_network](./Uebung_020f4_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **DigitalInput_I1 auf DataPanel_1A; BLINKER**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_020f4.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **AX_BLINK**: Instanz des Typs adapter::events::unidirectional::signals::AX_BLINK.
  - Parameter TIMELOW = T#1s
  - Parameter TIMEHIGH = T#1s200ms
- **DigitalOutput_1A**: Instanz des Typs DataPanel::io::MI::DQ::DataPanel_MI_QXA.
  - Parameter QI = TRUE
  - Parameter u8SAMember = MI_00
  - Parameter Output = DigitalOutput_1A

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- AX_BLINK.OUT -> DigitalOutput_1A.OUT

**Ereignisverbindungen:**
- DigitalOutput_1A.INITO -> AX_BLINK.START

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_020f4 demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
