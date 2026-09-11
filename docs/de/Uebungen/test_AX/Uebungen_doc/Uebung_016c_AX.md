# Uebung_016c_AX: Background Colour umschalten -- mit SubApp für SEL und INITVAL

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_016c_AX (Background Colour umschalten -- mit SubApp für SEL und INITVAL).

----

![Uebung_016c_AX_network](./Uebung_016c_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Background Colour umschalten -- mit SubApp für SEL und INITVAL**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_016c_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **SoftKey_UP_F1**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_UP_F2**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_RELEASED
- **AX_SR**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **Q_BackgroundColour_AUS**: Instanz des Typs isobus::UT::Q::Q_BackgroundColour_AUS.
  - Parameter u16ObjId = SoftKey_F7

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- AX_SR.Q -> Select_Colour.G
- Select_Colour.OUT -> Q_BackgroundColour_AUS.u8Colour

**Ereignisverbindungen:**

- SoftKey_UP_F1.IND -> AX_SR.S
- SoftKey_UP_F2.IND -> AX_SR.R

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_016c_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
