# Uebung_031_AX: LED Strip

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_031_AX (LED Strip).

----

![Uebung_031_AX_network](./Uebung_031_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **LED Strip**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_031_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **strip_set_pixel**: Instanz des Typs logiBUS::esp32::rgb::strip_set_pixel.
  - Parameter index = LED_strip::Output_strip
- **hsv2rgb**: Instanz des Typs logiBUS::esp32::rgb::hsv2rgb.
  - Parameter hue = 100
  - Parameter saturation = 100
  - Parameter value = 100

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- DigitalInput_CLK_I2.IND -> strip_set_pixel.clear
- hsv2rgb.CNF -> strip_set_pixel.set_pixel
- DigitalInput_CLK_I1.IND -> hsv2rgb.REQ

**Datenverbindungen:**

- hsv2rgb.r -> strip_set_pixel.red
- hsv2rgb.g -> strip_set_pixel.green
- hsv2rgb.b -> strip_set_pixel.blue

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_031_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
