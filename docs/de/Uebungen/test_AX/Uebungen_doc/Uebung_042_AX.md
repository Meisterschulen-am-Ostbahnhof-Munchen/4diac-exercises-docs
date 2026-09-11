# Uebung_042_AX: Scaling Function Block Testing

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_042_AX (Scaling Function Block Testing).

----

![Uebung_042_AX_network](./Uebung_042_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Scaling Function Block Testing**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_042_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **SCALE**: Instanz des Typs eclipse4diac::signalprocessing::SCALE.
  - Parameter IN = 10.0
  - Parameter MAX_IN = 20.0
  - Parameter MIN_IN = 4.0
  - Parameter MAX_OUT = 100.0
  - Parameter MIN_OUT = 0.0

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- DigitalInput_CLK_I1.IND -> SCALE.REQ

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_042_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
