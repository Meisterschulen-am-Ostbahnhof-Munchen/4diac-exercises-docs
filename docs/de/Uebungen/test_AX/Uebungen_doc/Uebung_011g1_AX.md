# Uebung_011g1_AX: Numeric Value Input -- multiple IOObservers

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_011g1_AX (Numeric Value Input -- multiple IOObservers).

----

![Uebung_011g1_AX_network](./Uebung_011g1_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Numeric Value Input -- multiple IOObservers**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_011g1_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **InputNumber_I1_1**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I1
- **InputNumber_I1_2**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I1
- **InputNumber_I1_3**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I1
- **AD_TO_AUDI_1**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.
- **AD_TO_AUDI_2**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.
- **AD_TO_AUDI_3**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AUDI.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- InputNumber_I1_1.IN -> AD_TO_AUDI_1.AD_IN
- InputNumber_I1_2.IN -> AD_TO_AUDI_2.AD_IN
- InputNumber_I1_3.IN -> AD_TO_AUDI_3.AD_IN

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_011g1_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
