# Uebung_016a_AX: Background Colour umschalten -- 3-fach

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_016a_AX (Background Colour umschalten -- 3-fach).

----

![Uebung_016a_AX_network](./Uebung_016a_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Background Colour umschalten -- 3-fach**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_016a_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **SoftKey_UP_F1**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_UP_F2**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_UP_F3**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_RELEASED
- **F_SEL_E_3**: Instanz des Typs adapter::selection::unidirectional::AUS_AUI_MUX_3.
- **Q_BackgroundColour**: Instanz des Typs isobus::UT::Q::Q_BackgroundColour_AUS.
  - Parameter u16ObjId = SoftKey_F7
- **initval_AUS**: Instanz des Typs adapter::types::unidirectional::AUS::initval::initval_AUS.
  - Parameter INIT_VAL = COLOR_WHITE
- **initval_AUS_1**: Instanz des Typs adapter::types::unidirectional::AUS::initval::initval_AUS.
  - Parameter INIT_VAL = COLOR_GREEN
- **initval_AUS_2**: Instanz des Typs adapter::types::unidirectional::AUS::initval::initval_AUS.
  - Parameter INIT_VAL = COLOR_RED
- **AUI_MUX_3**: Instanz des Typs adapter::events::unidirectional::AUI_MUX_3.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- F_SEL_E_3.OUT -> Q_BackgroundColour.u8Colour
- initval_AUS.OUT -> F_SEL_E_3.IN1
- initval_AUS_1.OUT -> F_SEL_E_3.IN2
- initval_AUS_2.OUT -> F_SEL_E_3.IN3
- AUI_MUX_3.K -> F_SEL_E_3.K

**Ereignisverbindungen:**

- SoftKey_UP_F1.IND -> AUI_MUX_3.EI1
- SoftKey_UP_F2.IND -> AUI_MUX_3.EI2
- SoftKey_UP_F3.IND -> AUI_MUX_3.EI3

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_016a_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
