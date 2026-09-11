# Uebung_032_AX: LED Strip Blinkende LED, mit Plug and Socket

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_032_AX (LED Strip Blinkende LED, mit Plug and Socket).

----

![Uebung_032_AX_network](./Uebung_032_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **LED Strip Blinkende LED, mit Plug and Socket**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_032_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **LED_GREEN_5HZ**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_strip_QXA.
  - Parameter QI = TRUE
  - Parameter Output = LED_strip::Output_strip
  - Parameter Colour = LED_COLOURS::LED_GREEN
  - Parameter FREQ = LED_FREQ::LED_5HZ
- **BUTTON_GREEN**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **BUTTON_YELLOW**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **LED_YELLOW_5HZ**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_strip_QXA.
  - Parameter QI = TRUE
  - Parameter Output = LED_strip::Output_strip
  - Parameter Colour = LED_COLOURS::LED_YELLOW
  - Parameter FREQ = LED_FREQ::LED_5HZ
- **BUTTON_RED**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
- **LED_RED_5HZ**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_strip_QXA.
  - Parameter QI = TRUE
  - Parameter Output = LED_strip::Output_strip
  - Parameter Colour = LED_COLOURS::LED_RED
  - Parameter FREQ = LED_FREQ::LED_5HZ
- **BUTTON_BLUE**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
- **LED_BLUE_5HZ**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_strip_QXA.
  - Parameter QI = TRUE
  - Parameter Output = LED_strip::Output_strip
  - Parameter Colour = LED_COLOURS::LED_BLUE
  - Parameter FREQ = LED_FREQ::LED_5HZ

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- BUTTON_GREEN.IN -> LED_GREEN_5HZ.OUT
- BUTTON_YELLOW.IN -> LED_YELLOW_5HZ.OUT
- BUTTON_RED.IN -> LED_RED_5HZ.OUT
- BUTTON_BLUE.IN -> LED_BLUE_5HZ.OUT

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_032_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
