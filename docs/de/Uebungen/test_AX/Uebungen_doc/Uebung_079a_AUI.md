# Uebung_079a_AUI: COGSOG Speed Over Ground auf UT ausgeben mit AR_Timeout_Zero (Adapter-Version)

![Uebung_079a_AUI_network](./Uebung_079a_AUI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung gibt die Geschwindigkeit über Grund (COGSOG Rapid Update, Speed Over Ground) des TECU auf dem ISOBUS-Terminal aus: mit PHYS-Skalierung (`0.036`) und der Fendt-Schaltung `AR_Timeout_Zero` (Adapter-Version, analog zur Krauternter-SubApp `TECU_COGSOG.SUB`). Bei einem Kommunikationsausfall wird `0.0` angezeigt.

## Verwendete Funktionsbausteine (FBs)

- **IA_COGSOGRapidUpdate_Inst**: ISOBUS-Adapter für Course Over Ground und Speed Over Ground (Rapid Update)
    - **Typ**: [`isobus::tecu::IA_COGSOGRapidUpdate`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_COGSOGRapidUpdate/)
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Liefert unter anderem die Geschwindigkeit über Grund als `AUI`-Adapter `SOG` und den Timeout-Status als `AX`-Adapter `TIMEOUT`.
- **Conv_COGSOG**: Skalierung des Feldbuswerts
    - **Typ**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED/)
    - **Parameter**: `SCALE = 0.036`, `OFFSET = 0`
    - **Erklärung**: Wandelt den `AUI`-Wert (UINT vom Feldbus) nur bei gültigem Signal in einen skalierten `AR`-Wert (REAL) um; `OFFSET` wird nach der Skalierung addiert.
- **TZ_COGSOG**: Fendt-Schaltung (SubApp)
    - **Typ**: `MyLib::sys::AR_Timeout_Zero`
    - **Parameter**: keine
    - **Erklärung**: Gibt `IN0` unverändert aus, außer `G` (hier `IA_COGSOGRapidUpdate_Inst.TIMEOUT`, echter Kommunikationsausfall) ist `TRUE`: Dann wird `0.0` statt des sonst eingefrorenen letzten Werts ausgegeben.
- **Q_NumericValue_COGSOG**: Anzeige auf dem ISOBUS-Terminal
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameter**: `stObj = NumberVariable_TECU_Speed_COGSOG_N`
    - **Erklärung**: Zeigt den physikalischen Wert (`rPhys`) im Objekt `NumberVariable_TECU_Speed_COGSOG_N` an.

## Programmablauf und Verbindungen

1. `IA_COGSOGRapidUpdate_Inst.SOG` → `Conv_COGSOG.IN` (AdapterConnection): Der Geschwindigkeitswert über Grund wird skaliert.
2. `Conv_COGSOG.OUT` → `TZ_COGSOG.IN0` (AdapterConnection): Der skalierte Wert ist der Live-Wert der Fendt-Schaltung.
3. `IA_COGSOGRapidUpdate_Inst.TIMEOUT` → `TZ_COGSOG.G` (AdapterConnection): Der Timeout-Status steuert, ob `0.0` ausgegeben wird.
4. `TZ_COGSOG.OUT` → `Q_NumericValue_COGSOG.rPhys` (AdapterConnection): Das Ergebnis wird auf dem Terminal angezeigt.

**Verhalten**: Solange der TECU die Botschaft liefert, zeigt das Terminal den mit `0.036` skalierten Wert. Fällt die Kommunikation aus (`TIMEOUT`), zeigt es `0.0`.

## Zusammenfassung

Die Übung entspricht im Aufbau `Uebung_070d_AUI` (WBSD), verwendet aber den Adapter `IA_COGSOGRapidUpdate` und den Skalierungsfaktor `0.036` für die Geschwindigkeit über Grund.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
