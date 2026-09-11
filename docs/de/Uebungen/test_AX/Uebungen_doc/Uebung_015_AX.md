# Uebung_015_AX: Object Pointer umschalten

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_015_AX (Object Pointer umschalten).

----

![Uebung_015_AX_network](./Uebung_015_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Object Pointer umschalten**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_015_AX.SUB, welche die folgende Bausteinstruktur verwendet:

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
- **F_UINT_TO_UDINT**: Instanz des Typs adapter::types::unidirectional::AUDI::initval::initval_AUDI.
  - Parameter INIT_VAL = Button_A1
- **F_SEL**: Instanz des Typs adapter::iec61131::selection::AUDI_AX_SEL_AUDI.
- **Q_NumericValue_AUDI**: Instanz des Typs isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = ObjectPointer_P1
- **initval_AUDI**: Instanz des Typs adapter::types::unidirectional::AUDI::initval::initval_AUDI.
  - Parameter INIT_VAL = ID_NULL

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- F_UINT_TO_UDINT.OUT -> F_SEL.IN1
- AX_SR.Q -> F_SEL.G
- initval_AUDI.OUT -> F_SEL.IN0
- F_SEL.OUT -> Q_NumericValue_AUDI.u32NewValue

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

Die Übung Uebung_015_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
