# Uebung_011e_MIX_AX: Numeric Value Input I1 Durchschleifen auf N3 (Software Scale via NumericObjectPool_S) falsch gemischt

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_011e_MIX_AX (Numeric Value Input I1 Durchschleifen auf N3 (Software Scale via NumericObjectPool_S) falsch gemischt!).

----

![Uebung_011e_MIX_AX_network](./Uebung_011e_MIX_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Numeric Value Input I1 Durchschleifen auf N3 (Software Scale via NumericObjectPool_S) falsch gemischt!**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_011e_MIX_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **InputNumber_I1**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_IDA.
  - Parameter QI = TRUE
  - Parameter u16ObjId = InputNumber_I1
- **AD_TO_AR_NUM**: Instanz des Typs adapter::conversion::unidirectional::AD_TO_AR_NUM.
- **Q_NumericValue_PHYS**: Instanz des Typs isobus::UT::Q::Q_NumericValue_PHYSA.
  - Parameter stObj = OutputNumber_N3_N

### Verbindungen und Schnittstellen

**Adapterverbindungen:**

- InputNumber_I1.IN -> AD_TO_AR_NUM.AD_IN
- AD_TO_AR_NUM.AR_OUT -> Q_NumericValue_PHYS.rPhys

### Hinweise aus dem Modell

> Beispiel: I1-Eingabe 10 → F_RAW_TO_PHYS(I1) → 10.0 → Q_NumericValue_PHYS(N3) → N3 zeigt 10.00.

die beiden Namespaces sind INKOMPATIBEL !!!
> Uebungen::const::UT::DefaultPool::InputNumber_I1
> Uebungen::const::UT::DefaultPool_Numeric::OutputNumber_N3_N

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_011e_MIX_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
