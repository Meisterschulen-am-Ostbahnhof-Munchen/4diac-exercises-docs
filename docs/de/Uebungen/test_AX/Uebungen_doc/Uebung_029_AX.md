# Uebung_029_AX: LED_DO Blinkende LED, mit Plug and Socket

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_029_AX (LED_DO Blinkende LED, mit Plug and Socket).

----

![Uebung_029_AX_network](./Uebung_029_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **LED_DO Blinkende LED, mit Plug and Socket**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_029_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **LED_Q1_5HZ**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_DO_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
  - Parameter FREQ = LED_FREQ::LED_5HZ
- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **LED_Q1_1HZ**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_DO_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
  - Parameter FREQ = LED_FREQ::LED_1HZ
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **LED_Q1_ON**: Instanz des Typs logiBUS::io::DO_LED::logiBUS_LED_DO_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
  - Parameter FREQ = LED_FREQ::LED_ON
- **DigitalInput_I3**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- DigitalInput_I1.IN -> LED_Q1_5HZ.OUT
- DigitalInput_I2.IN -> LED_Q1_1HZ.OUT
- DigitalInput_I3.IN -> LED_Q1_ON.OUT

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_029_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
