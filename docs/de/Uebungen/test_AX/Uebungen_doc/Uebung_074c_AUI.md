# Uebung_074c_AUI: RPTO Speed + SpeedSet auf UT ausgeben mit AR_Timeout_Zero (Adapter-Version)

![Uebung_074c_AUI_network](./Uebung_074c_AUI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung gibt die Drehzahl (`SPEED`) und den Sollwert (`SPEED_SET`) der hinteren Zapfwelle (RPTO, Rear PTO) des TECU auf dem Universal Terminal (UT) aus. Beide Rohwerte werden mit `SCALE = 0.125` in physikalische `REAL`-Werte skaliert und jeweils durch eine eigene Instanz der SubApp `AR_Timeout_Zero` (Fendt-Schaltung) geführt: Bei einem Kommunikationsausfall (`IA_RPTO.TIMEOUT`) wird `0.0` ausgegeben. Da das Timeout-Signal zwei Ziele hat, wird es über `AX_SPLIT_2` verteilt. Alle Verbindungen sind Adapter (analog zu `TECU_RPTO.SUB` im Projekt Krauternter).

## Verwendete Funktionsbausteine (FBs)

- **IA_RPTO**: TECU-Eingang RPTO
    - **Typ**: [`isobus::tecu::IA_RPTO`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_RPTO/)
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Liefert `SPEED`, `SPEED_SET` und `TIMEOUT` als Adapter.
- **Conv_Speed**, **Conv_SpeedSet**: Skalierung der Rohwerte
    - **Typ**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED/)
    - **Parameter**: jeweils `SCALE = 0.125`, `OFFSET = 0`
    - **Erklärung**: `Conv_Speed` skaliert `SPEED`, `Conv_SpeedSet` skaliert `SPEED_SET`.
- **AX_SPLIT_2_Timeout**: Verteilung des Timeout-Signals
    - **Typ**: [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/)
    - **Parameter**: keine
    - **Erklärung**: `OUT1` geht an `TZ_Speed.G`, `OUT2` an `TZ_SpeedSet.G`.
- **TZ_Speed**, **TZ_SpeedSet**: Fendt-Schaltung (SubApps)
    - **Typ**: `MyLib::sys::AR_Timeout_Zero` (SubApp-Instanzen)
    - **Parameter**: keine
    - **Erklärung**: `OUT` liefert `IN0` unverändert, außer `G` ist `TRUE` (Timeout), dann `0.0`.
- **Q_NumericValue_Speed**, **Q_NumericValue_SpeedSet**: numerische Anzeigen
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameter**: `stObj = NumberVariable_TECU_RPTO_Speed_N` bzw. `NumberVariable_TECU_RPTO_SpeedSet_N`
    - **Erklärung**: Zeigen die physikalischen Werte aus `rPhys` an den jeweiligen UT-Objekten an.

## Programmablauf und Verbindungen

1. `IA_RPTO.SPEED` → `Conv_Speed.IN` → `TZ_Speed.IN0`, `TZ_Speed.OUT` → `Q_NumericValue_Speed.rPhys` (AdapterConnections): Pfad der Drehzahl.
2. `IA_RPTO.SPEED_SET` → `Conv_SpeedSet.IN` → `TZ_SpeedSet.IN0`, `TZ_SpeedSet.OUT` → `Q_NumericValue_SpeedSet.rPhys` (AdapterConnections): Pfad des Sollwerts.
3. `IA_RPTO.TIMEOUT` → `AX_SPLIT_2_Timeout.IN`, `AX_SPLIT_2_Timeout.OUT1` → `TZ_Speed.G` und `AX_SPLIT_2_Timeout.OUT2` → `TZ_SpeedSet.G` (AdapterConnections): Ein Timeout schaltet beide Ausgaben auf `0.0`.

## Zusammenfassung

Die Übung 074c_AUI erweitert `Uebung_072d_AUI` auf zwei Werte (Drehzahl und Sollwert der hinteren Zapfwelle). Weil ein Adapter-Plug nur ein Ziel hat, verteilt `AX_SPLIT_2` das gemeinsame Timeout-Signal auf beide `AR_Timeout_Zero`-Instanzen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
