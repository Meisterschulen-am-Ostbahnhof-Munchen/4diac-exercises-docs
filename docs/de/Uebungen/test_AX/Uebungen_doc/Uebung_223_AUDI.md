# Uebung_223_AUDI: Standard IEC 61131-3 AUDI_FB_CTUD (Adapter-Version, Vorwärts-/Rückwärtszähler, UDINT) mit Terminal-Ausgabe

![Uebung_223_AUDI_network](./Uebung_223_AUDI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung zeigt einen Vorwärts-/Rückwärtszähler (CTUD) nach IEC 61131-3 als Adapter-Version für den Datentyp UDINT. Vier digitale Eingänge steuern Aufwärtszählen, Abwärtszählen, Reset und Laden, der feste Vorgabewert `5` kommt aus einem `initval_AUDI`-Konstantenbaustein. Der Zählerstand wird auf dem ISOBUS-Terminal angezeigt, die Zählerausgänge `QU` und `QD` liegen an `Q1` und `Q2`.

## Verwendete Funktionsbausteine (FBs)

- **Input_CU**: Eingang Vorwärtszählen
    - **Typ**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`
    - **Erklärung**: Stellt den Zustand von `I1` als Adapterausgang `IN` bereit.
- **Input_CD**: Eingang Rückwärtszählen
    - **Typ**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`
    - **Erklärung**: Stellt den Zustand von `I2` als Adapterausgang `IN` bereit.
- **Input_R**: Reset-Eingang
    - **Typ**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I3`
    - **Erklärung**: Stellt den Zustand von `I3` als Adapterausgang `IN` bereit.
- **Input_LD**: Eingang Laden
    - **Typ**: [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I4`
    - **Erklärung**: Stellt den Zustand von `I4` als Adapterausgang `IN` bereit.
- **initval_AUDI**: Konstante für den Vorgabewert
    - **Typ**: [`adapter::types::unidirectional::AUDI::initval::initval_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UDINT/initval/initval_AUDI/)
    - **Parameter**: `INIT_VAL = 5`
    - **Erklärung**: Liefert den Vorgabewert `5` als `AUDI`-Adapterwert an `AUDI_FB_CTUD.PV`.
- **AUDI_FB_CTUD**: Vorwärts-/Rückwärtszähler (UDINT, Adapter-Version)
    - **Typ**: [`adapter::iec61131::counters::AUDI_FB_CTUD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/counters/AUDI_FB_CTUD/)
    - **Parameter**: keine
    - **Erklärung**: Zählt über `CU` aufwärts und über `CD` abwärts, `R` setzt zurück, `LD` lädt den Vorgabewert `PV`. `QU` und `QD` sind die Zählerausgänge nach IEC 61131-3 (CTUD), `CV` liefert den aktuellen Zählerstand.
- **Output_QU**: Digitalausgang für `QU`
    - **Typ**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt `AUDI_FB_CTUD.QU` am physischen Ausgang `Q1` aus.
- **Output_QD**: Digitalausgang für `QD`
    - **Typ**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q2`
    - **Erklärung**: Gibt `AUDI_FB_CTUD.QD` am physischen Ausgang `Q2` aus.
- **Q_NumericValue_AUDI**: Anzeige auf dem ISOBUS-Terminal
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameter**: `u16ObjId = OutputNumber_N1`
    - **Erklärung**: Empfängt den Zählerstand über den `AUDI`-Adapter `u32NewValue` und zeigt ihn im Objekt `OutputNumber_N1` an.

## Programmablauf und Verbindungen

1. `Input_CU.IN` → `AUDI_FB_CTUD.CU`, `Input_CD.IN` → `AUDI_FB_CTUD.CD`, `Input_R.IN` → `AUDI_FB_CTUD.R` und `Input_LD.IN` → `AUDI_FB_CTUD.LD` (AdapterConnections): Die Zustände von `I1` bis `I4` steuern Zählen, Rückwärtszählen, Reset und Laden.
2. `initval_AUDI.OUT` → `AUDI_FB_CTUD.PV` (AdapterConnection): Der Vorgabewert `5` wird als Preset übergeben.
3. `AUDI_FB_CTUD.QU` → `Output_QU.OUT` und `AUDI_FB_CTUD.QD` → `Output_QD.OUT` (AdapterConnections): Die Zählerausgänge liegen an `Q1` und `Q2`.
4. `AUDI_FB_CTUD.CV` → `Q_NumericValue_AUDI.u32NewValue` (AdapterConnection): Der Zählerstand wird auf dem Terminal angezeigt.

**Verhalten**: `I1` zählt aufwärts, `I2` abwärts, `I3` setzt den Zähler zurück und `I4` lädt den Vorgabewert `5`. Der Zählerstand erscheint in `OutputNumber_N1`. Laut Netzwerk-Kommentar kann hier gegebenenfalls je ein `AX_D_FF` eingebaut werden, damit die Events reduziert werden.

## Zusammenfassung

Die Übung zeigt die adapterbasierte Anbindung eines IEC-61131-3-Auf-/Abwärtszählers: Eingänge, Vorgabewert, Zählerausgänge und Anzeige sind rein über Adapterverbindungen verschaltet.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
