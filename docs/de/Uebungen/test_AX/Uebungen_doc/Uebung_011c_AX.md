# Uebung_011c_AX: Numeric Value Input I3 Durchschleifen auf N3

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_011c_AX (Numeric Value Input I3 Durchschleifen auf N3).

----

![Uebung_011c_AX_network](./Uebung_011c_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Numeric Value Input I3 Durchschleifen auf N3**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_011c_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **InputNumber_I3_N**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I3
- **AD_TO_AUDI**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.
- **Q_NumericValue**: Instanz des Typs isobus::UT::Q::Q_NumericValue_AUDI.
  - Parameter u16ObjId = OutputNumber_N3

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- InputNumber_I3_N.IN -> AD_TO_AUDI.AD_IN
- AD_TO_AUDI.AUDI_OUT -> Q_NumericValue.u32NewValue

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_011c_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
