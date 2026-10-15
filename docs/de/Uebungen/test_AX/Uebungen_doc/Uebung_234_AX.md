# Uebung_234_AX: Zweipunktregler mit Hysterese (reine Hardware-Übung, ohne VT)

![Uebung_234_AX_network](./Uebung_234_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung implementiert ein klassisches Thermostat-/Füllstandsschalter-Muster: Ein analoger Messwert (z. B. Füllstand oder Temperatur) wird gegen einen Mittelwert `MI` mit Totzone (`DEAD`) und Hysterese (`HYSTERESIS`) geprüft und steuert darüber zwei entgegengesetzte Aktoren (z. B. Befüllen/Entleeren oder Heizen/Kühlen) an – nie beide gleichzeitig. Die Übung arbeitet ausschließlich mit physischer Hardware, ohne VT-Anzeige.

## Verwendete Funktionsbausteine (FBs)

- **AnalogInput_I4**: Analogeingang
    - **Typ**: [`logiBUS::io::AI::logiBUS_AI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/AI/logiBUS_AI_IDA/)
    - **Parameter**: `QI = TRUE`, `Input = AnalogInput_I4`, `AnalogInput_hysteresis = 5`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Erklärung**: Liest den rohen Analogmesswert (Adapter `IN`, Typ AD) ein und filtert kleine Signalschwankungen über die eingangsseitige Hysterese von 5.
- **AD_TO_AR_NUM**: Numerische Konvertierung von AD nach AR
    - **Typ**: [`adapter::conversion::unidirectional::AD_TO_AR_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AD_TO_AR_NUM/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt den rohen Messwert (AD) numerisch (AD → UDINT → REAL) in einen `REAL`-Adapter (`AR`) um – wie in `Uebung_028a_AR` ("ein AD_TO_AR wäre wie ein reinterpret_cast").
- **HysteresisParams_AR**: Parameter-Baustein für die Hysterese-Schwellwerte (SubApp)
    - **Typ**: [`MyLib::sys::HysteresisParams_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/HysteresisParams_AR/)
    - **Parameter**: `rMI = 500.0`, `rDEAD = 20.0`, `rHYSTERESIS = 30.0`
    - **Erklärung**: Bündelt `MI`, `DEAD` und `HYSTERESIS` als drei feste AR-Konstanten (je ein `initval_AR`); die Werte sind am Aufruf einstellbar. Hier sind es feste Beispielwerte, in echten Anlagen wären sie typischerweise per INI/OPC-UA parametrierbar.
- **DualHysteresis**: Zweipunktregler mit Totzone und Hysterese
    - **Typ**: [`logiBUS::signalprocessing::hysteresis::DualHysteresis_AR_A2X`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/signalprocessing/hysteresis/DualHysteresis_AR_A2X/)
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Vergleicht den Messwert (`INPUT`) gegen `MI`: Steigt der Wert über `MI + DEAD + HYSTERESIS` (hier 550), schaltet `UP` ein; fällt er unter `MI - DEAD - HYSTERESIS` (hier 450), schaltet `DOWN` ein. Ausgeschaltet wird erst wieder, wenn der Wert in die reine Totzone `MI ± DEAD` (480–520) zurückkehrt – die Differenz zwischen Ein- und Ausschaltpunkt verhindert Flattern nahe der Schaltschwelle.
- **A2X_TO_QXA2**: Entbündeln auf zwei Ausgänge (SubApp)
    - **Typ**: [`MyLib::sys::A2X_TO_QXA2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X_TO_QXA2/)
    - **Parameter**: `Output_UP = Output_Q1`, `Output_DOWN = Output_Q2`
    - **Erklärung**: Entbündelt das eine `A2X`-Ausgangssignal (`UP`/`DOWN`) von `DualHysteresis` auf zwei physische logiBUS-Ausgänge (`Q1` für UP, `Q2` für DOWN).

## Programmablauf und Verbindungen

1. `AnalogInput_I4.IN` → `AD_TO_AR_NUM.AD_IN`: Der Rohwert wird numerisch in einen `AR`-Wert (REAL-Adapter) gewandelt.
2. `AD_TO_AR_NUM.AR_OUT` → `DualHysteresis.INPUT`: Der aufbereitete Messwert gelangt an den Zweipunktregler.
3. `HysteresisParams_AR.MI` / `.DEAD` / `.HYSTERESIS` → `DualHysteresis.MI` / `.DEAD` / `.HYSTERESIS`: Die Schwellwerte (500.0 / 20.0 / 30.0) werden fest vorgegeben.
4. `DualHysteresis.OUT` → `A2X_TO_QXA2.IN`: Das kombinierte UP/DOWN-Ergebnis wird an den Ausgabe-Baustein übergeben.
5. `A2X_TO_QXA2` schreibt UP auf `Output_Q1` und DOWN auf `Output_Q2` – die beiden Aktoren werden nie gleichzeitig aktiviert.

Alle Verbindungen sind Adapterverbindungen.

## Zusammenfassung

Die Übung 234_AX zeigt den klassischen Aufbau eines Zweipunktreglers mit Hysterese rein auf Hardware-Ebene: Ein Analogeingang wird über den numerischen Adapterkonverter `AD_TO_AR_NUM` aufbereitet, von `DualHysteresis_AR_A2X` gegen einen Mittelwert mit Totzone und Hysterese geprüft und über `A2X_TO_QXA2` auf zwei sich gegenseitig ausschließende Digitalausgänge geschaltet. Für dieselbe Funktion mit VT-Anzeige (Messwert als Zahlenfeld, UP/DOWN als Hintergrundfarbe statt physischer Ausgänge) siehe `Uebung_235_AX`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
