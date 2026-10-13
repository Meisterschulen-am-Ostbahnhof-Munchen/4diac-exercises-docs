# Uebung_072d_AUI: GBSD auf UT ausgeben mit AR_Timeout_Zero (Adapter-Version)

![Uebung_072d_AUI_network](./Uebung_072d_AUI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung gibt die GBSD-Geschwindigkeit des TECU auf dem Universal Terminal (UT) aus. Der Rohwert wird mit `SCALE = 0.0036` in einen physikalischen `REAL`-Wert skaliert und durch die SubApp `AR_Timeout_Zero` geführt (Fendt-Schaltung): Bei einem echten Kommunikationsausfall (`IA_GBSD.TIMEOUT`) wird `0.0` ausgegeben statt des sonst einfach eingefrorenen letzten Werts. Alle Verbindungen sind Adapter (Adapter-Version, analog zu `TECU_GBSD.SUB` im Projekt Krauternter).

## Verwendete Funktionsbausteine (FBs)

- **IA_GBSD**: TECU-Eingang GBSD
    - **Typ**: [`isobus::tecu::IA_GBSD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_GBSD/)
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Liefert `SPEED` (Rohwert) und `TIMEOUT` (Kommunikationsausfall) als Adapter.
- **Conv_GBSD**: Skalierung des Rohwerts
    - **Typ**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED/)
    - **Parameter**: `SCALE = 0.0036`, `OFFSET = 0`
    - **Erklärung**: Wandelt den `UINT`-Rohwert (`IN`) mit Skalierung und Offset in einen `REAL`-Adapterwert (`OUT`).
- **TZ_GBSD**: Fendt-Schaltung (SubApp)
    - **Typ**: `MyLib::sys::AR_Timeout_Zero` (SubApp-Instanz)
    - **Parameter**: keine
    - **Erklärung**: `OUT` liefert `IN0` unverändert, außer `G` ist `TRUE` (Timeout), dann `0.0`.
- **Q_NumericValue**: numerische Anzeige
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameter**: `stObj = NumberVariable_TECU_Speed_GBSD_N`
    - **Erklärung**: Zeigt den physikalischen Wert aus `rPhys` am UT-Objekt `NumberVariable_TECU_Speed_GBSD_N` an.

## Programmablauf und Verbindungen

1. `IA_GBSD.SPEED` → `Conv_GBSD.IN` (AdapterConnection): Der Rohwert wird skaliert.
2. `Conv_GBSD.OUT` → `TZ_GBSD.IN0` (AdapterConnection): Der skalierte Wert ist der Live-Wert der Fendt-Schaltung.
3. `IA_GBSD.TIMEOUT` → `TZ_GBSD.G` (AdapterConnection): Ein Timeout schaltet die Ausgabe auf `0.0`.
4. `TZ_GBSD.OUT` → `Q_NumericValue.rPhys` (AdapterConnection): Das Ergebnis wird am UT angezeigt.

## Zusammenfassung

Die Übung 072d_AUI zeigt die Skalierung eines TECU-Rohwerts mit `AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED` und die Absicherung gegen Kommunikationsausfall mit der SubApp `AR_Timeout_Zero`: Fällt die Kommunikation aus, zeigt das UT `0.0` statt eines eingefrorenen Werts.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
