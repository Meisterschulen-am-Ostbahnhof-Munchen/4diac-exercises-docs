# Uebung_HystereseVertikal_AX_withAnalog: Hysterese-Schwellen im vertikaler Bargraph

![Uebung_HystereseVertikal_AX_withAnalog_network](./Uebung_HystereseVertikal_AX_withAnalog_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_HystereseVertikal_AX_withAnalog` zeigt eine **Zwei-Wege-Hysterese** (Heben/Senken) mit analogem Istwert und Bedienung über das ISOBUS-Terminal: Über die Eingabefelder **MID**, **DEAD** und **HYSTERESIS** werden Mittelwert, Totzone und Hysterese vorgegeben. Daraus werden fünf Schwellwerte berechnet, die als fünf Dreiecke (Polygone) live im vertikalen Bargraph (von unten nach oben) mitwandern. Der Istwert vom Analogeingang `AnalogInput_I4` wird als Zahl angezeigt und steuert über die Hysterese die Ausgänge `Output_Q1` (hoch) und `Output_Q2` (runter). Beim vertikalen Bargraph (Typ „BottomToTop“) wird die Pixelposition gespiegelt: ein hoher Wert ergibt einen kleineren `Top`-Wert; dafür bekommt `Q_ChildPosition_AR_Bargraph_BottomToTop` zusätzlich die Bargraph-Länge (`LengthPx`).

Die Schnittstelle der SubApp ist leer. Die Pool-Objekte stammen aus `Uebungen::const::UT::HystereseVertikal` (`DefaultPool`, `DefaultPool_Numeric`, `DefaultPool_Bargraph`).

## Verwendete Funktionsbausteine (FBs)

### Eingabefelder und Istwert

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `MID_N` | [NumericValue_PHYSA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_PHYSA/) | `stObj` = `InputNumber_MID_N` | Eingabefeld Mittelwert (MID), liefert `rPhys` |
| `DEAD_N` | [NumericValue_PHYSA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_PHYSA/) | `stObj` = `InputNumber_DEAD_N` | Eingabefeld Totzone (DEAD) |
| `HYST_N` | [NumericValue_PHYSA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_PHYSA/) | `stObj` = `InputNumber_HYSTERESIS_N` | Eingabefeld Hysterese |
| `AnalogInput_I4` | [logiBUS_AI_IDA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/AI/logiBUS_AI_IDA/) | `Input` = `AnalogInput_I4`, `AnalogInput_hysteresis` = `DWORD#0`, `TimeDelta` = `DWORD#250`, `TimeRateLimit` = `DWORD#100` | Analogeingang I4 |
| `AD_TO_AUDI` | [AD_TO_AUDI](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AUDI/AD_TO_AUDI/) | – | Adapter-Konvertierung `AD` → `AUDI` |
| `AUDI_TO_AR` | [AUDI_TO_AR](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AR_AUDI/AUDI_TO_AR/) | – | Adapter-Konvertierung `AUDI` → `AR` (REAL) |
| `Mul_Istwert` | [AR_R_MUL](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/AR_R_MUL/) | `FACTOR` = `REAL#0.024420024420024420` | Skaliert den Rohwert (Faktor = 100/4095) |
| `Split_Istwert` | [AR_SPLIT_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/REAL/AR_SPLIT_2/) | – | Verteilt den Istwert auf Anzeige und Hysterese |
| `Q_Istwert` | [Q_NumericValue_PHYSA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/) | `stObj` = `(u16ObjId := NumberVariable_Istwert, r32Scale := 1.0, i32Offset := 0, u8Decimals := 0)` | Zeigt den Istwert im Terminal an |

### Schwellwerte und Hysterese

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Thresholds` | [DualHysteresis_Thresholds_AR](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/DualHysteresis_Thresholds_AR/) | – | Berechnet aus MID, DEAD und HYST die fünf Schwellen (MID, MID±DEAD, MID±(DEAD+HYST)) |
| `Split_MID_OUT` | [AR_SPLIT_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/REAL/AR_SPLIT_2/) | – | Verteilt `MID_OUT` auf den Mittelwert-Marker und die Hysterese |
| `DualHysteresis` | [DualHysteresis_AR_AX](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/signalprocessing/hysteresis/DualHysteresis_AR_AX/) | `QI` = `TRUE` | Zwei-Wege-Hysterese mit `INPUT`, `MI`, `DEAD`, `HYSTERESIS`; Ausgänge `DO_UP`, `DO_DOWN` |
| `Output_Hoch` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q1` | Ausgang „hoch" |
| `Output_Runter` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q2` | Ausgang „runter" |

### Schwellwert-Dreiecke im Bargraph

| Instanz | Typ | Parameter (`u16ObjId`) | Erklärung |
|---|---|---|---|
| `QPos_MID` | [Q_ChildPosition_AR_Bargraph_BottomToTop](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Q_ChildPosition_AR_Bargraph_BottomToTop/) | `Polygon_MID` | Marker für MID |
| `QPos_MID_PLUS_DEAD` | [Q_ChildPosition_AR_Bargraph_BottomToTop](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Q_ChildPosition_AR_Bargraph_BottomToTop/) | `Polygon_MID_PLUS_DEAD` | Marker für MID + DEAD |
| `QPos_MID_MINUS_DEAD` | [Q_ChildPosition_AR_Bargraph_BottomToTop](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Q_ChildPosition_AR_Bargraph_BottomToTop/) | `Polygon_MID_MINUS_DEAD` | Marker für MID − DEAD |
| `QPos_MID_PLUS_DEAD_HYST` | [Q_ChildPosition_AR_Bargraph_BottomToTop](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Q_ChildPosition_AR_Bargraph_BottomToTop/) | `Polygon_MID_PLUS_DEAD_HYST` | Marker für MID + (DEAD + HYST) |
| `QPos_MID_MINUS_DEAD_HYST` | [Q_ChildPosition_AR_Bargraph_BottomToTop](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Q_ChildPosition_AR_Bargraph_BottomToTop/) | `Polygon_MID_MINUS_DEAD_HYST` | Marker für MID − (DEAD + HYST) |

Gemeinsame Parameter aller fünf `QPos_*`-Instanzen: `u16ObjId`, `u16ObjIdParent` = `Container_Schwellwerte`, `xScale` = `TRUE`, `ScalePx` = `Bargraph_Hysterese_ScalePx`, `LengthPx` = `Bargraph_Hysterese_LengthPx`.

## Programmablauf und Verbindungen

1. **Vorgabe**: `MID_N.rPhys`, `DEAD_N.rPhys` und `HYST_N.rPhys` gehen auf `Thresholds.MI`, `.DEAD` und `.HYST`.
2. **Schwellen → Anzeige**: Die vier abgeleiteten Schwellen (`MID_PLUS_DEAD`, `MID_MINUS_DEAD`, `MID_PLUS_DEAD_HYST`, `MID_MINUS_DEAD_HYST`) gehen jeweils auf `rPhys` des zugehörigen `QPos_*`; `MID_OUT` läuft über `Split_MID_OUT` (`OUT1` → `QPos_MID.rPhys`, `OUT2` → `DualHysteresis.MI`). Verschiebt der Bediener ein Eingabefeld, wandern die Dreiecke live mit.
3. **Hysterese-Parameter**: `Thresholds.DEAD_OUT` → `DualHysteresis.DEAD`, `Thresholds.HYST_OUT` → `DualHysteresis.HYSTERESIS`.
4. **Istwert**: `AnalogInput_I4.IN` → `AD_TO_AUDI` → `AUDI_TO_AR` → `Mul_Istwert` → `Split_Istwert`; `OUT1` → `Q_Istwert.rPhys` (Anzeige), `OUT2` → `DualHysteresis.INPUT`.
5. **Ausgänge**: `DualHysteresis.DO_UP` → `Output_Hoch` (Q1), `DualHysteresis.DO_DOWN` → `Output_Runter` (Q2).

Versionshistorie laut Quelle: V1.1 – AR_TO_AI, Zero und QPos in die SubApp `Q_ChildPosition_AR_*` ausgelagert; V1.2 – Schwellwertberechnung in `DualHysteresis_Thresholds_AR` ausgelagert.

## Zusammenfassung

`Uebung_HystereseVertikal_AX_withAnalog` verbindet drei Eingabefelder, eine Analogmessung, eine Zwei-Wege-Hysterese und die Live-Positionierung von fünf Schwellwert-Dreiecken in einem vertikaler Bargraph – komplett über Adapter-Verbindungen, ohne Ereignisverdrahtung.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
