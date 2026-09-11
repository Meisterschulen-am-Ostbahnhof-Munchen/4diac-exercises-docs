# Uebung_015b: Object Pointer umschalten -- 3-fach

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_015b (Object Pointer umschalten -- 3-fach).

----

![Uebung_015b_network](./Uebung_015b_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Object Pointer umschalten -- 3-fach**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_015b.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **SoftKey_UP_F1**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_UP_F2**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_RELEASED
- **F_UINT_TO_UDINT**: Instanz des Typs iec61131::conversion::F_UINT_TO_UDINT.
  - Parameter IN = Button_A1
- **F_SEL_E_3**: Instanz des Typs eclipse4diac::utils::selection::F_SEL_E_3.
  - Parameter IN1 = ID_NULL
- **Q_NumericValue**: Instanz des Typs isobus::UT::Q::Q_NumericValue.
  - Parameter u16ObjId = ObjectPointer_P1
- **SoftKey_UP_F3**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_RELEASED
- **F_UINT_TO_UDINT_1**: Instanz des Typs iec61131::conversion::F_UINT_TO_UDINT.
  - Parameter IN = Button_A2
- **INIT**: Instanz des Typs iec61131::booleanOperators::INIT.

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**
- F_SEL_E_3.CNF -> Q_NumericValue.REQ
- SoftKey_UP_F1.IND -> F_SEL_E_3.REQ1
- SoftKey_UP_F2.IND -> F_SEL_E_3.REQ2
- SoftKey_UP_F3.IND -> F_SEL_E_3.REQ3
- INIT.INITO -> INIT.REQ
- INIT.CNF -> F_UINT_TO_UDINT.REQ
- INIT.CNF -> F_UINT_TO_UDINT_1.REQ

**Datenverbindungen:**
- F_UINT_TO_UDINT.OUT -> F_SEL_E_3.IN2
- F_SEL_E_3.OUT -> Q_NumericValue.u32NewValue
- F_UINT_TO_UDINT_1.OUT -> F_SEL_E_3.IN3

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_015b demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
