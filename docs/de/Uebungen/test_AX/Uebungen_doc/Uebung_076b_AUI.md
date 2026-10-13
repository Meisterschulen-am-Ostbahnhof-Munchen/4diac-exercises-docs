# Uebung_076b_AUI: MSS mit Compound Scaling auf UT ausgeben (Adapter-Version) ohne Fendt-Schaltung

![Uebung_076b_AUI_network](./Uebung_076b_AUI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung gibt die maschinenausgewählte Geschwindigkeit (Machine Selected Speed, MSS) des TECU auf dem ISOBUS-Terminal aus. Der `AUI`-Wert wird mit Compound Scaling (getrennte Faktoren für oberes und unteres Byte) in einen physikalischen Wert gewandelt. Es gibt keine Fendt-Schaltung: Bei einem Timeout wird nicht auf `0.0` umgeschaltet. Die Übung ist die Adapter-Version von `Uebung_076` ohne Fendt-Schaltung.

## Verwendete Funktionsbausteine (FBs)

- **IA_MSS**: ISOBUS-Adapter für Machine Selected Speed (MSS)
    - **Typ**: [`isobus::tecu::IA_MSS`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_MSS/)
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Liefert die maschinenausgewählte Geschwindigkeit als `AUI`-Adapter `SPEED`.
- **COMPOUND_SCALE**: Compound-Skalierung
    - **Typ**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_COMPOUND_SCALE/)
    - **Parameter**: `SCALE_HIGH = REAL#0.256`, `SCALE_LOW = REAL#0.001`, `OFFSET = DINT#0`
    - **Erklärung**: Wandelt den `AUI`-Wert nur bei gültigem Signal mit getrennten Skalierungsfaktoren für oberes (`SCALE_HIGH`) und unteres Byte (`SCALE_LOW`) in einen `AR`-Wert um; `OFFSET` wird nach der Skalierung addiert.
- **Q_NumericValue**: Anzeige auf dem ISOBUS-Terminal
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameter**: `stObj = NumberVariable_Wheel_based_machine_speed_N`
    - **Erklärung**: Zeigt den physikalischen Wert (`rPhys`) im Objekt `NumberVariable_Wheel_based_machine_speed_N` an. Laut Netzwerk-Kommentar ist das ein Ersatz: `NumberVariable_Machine_selected_speed` muss noch im Object Pool angelegt und hier eingetragen werden.

## Programmablauf und Verbindungen

1. `IA_MSS.SPEED` → `COMPOUND_SCALE.IN` (AdapterConnection): Der Geschwindigkeitswert vom TECU wird skaliert.
2. `COMPOUND_SCALE.OUT` → `Q_NumericValue.rPhys` (AdapterConnection): Der skalierte Wert wird auf dem Terminal angezeigt.

**Verhalten**: Das Terminal zeigt den skalierten MSS-Wert an. Der Timeout-Adapter `IA_MSS.TIMEOUT` ist nicht verdrahtet.

## Zusammenfassung

Die Übung zeigt die kürzeste Kette für einen TECU-Wert mit zweistufiger Skalierung: TECU-Adapter → Compound Scaling → Anzeige. Der noch fehlende Objekt-Pool-Eintrag `NumberVariable_Machine_selected_speed` ist im Netzwerk als TODO vermerkt.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
