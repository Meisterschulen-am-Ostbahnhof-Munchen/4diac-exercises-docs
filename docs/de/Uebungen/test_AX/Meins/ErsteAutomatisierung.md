# ErsteAutomatisierung: Motor Links-/Rechtslauf

![ErsteAutomatisierung_network](./ErsteAutomatisierung_network.svg)

* * * * * * * * * *

## Einleitung

`ErsteAutomatisierung` ist eine frühe Übung (Kommentar im Typ: "Motor Links/Rechtslauf"). Aus drei Tastern (`Input_I1`…`Input_I3`) wird über zwei Set/Reset-Speicher die Ansteuerung zweier Ausgänge (`Output_Q5`, `Output_Q6`) für die beiden Drehrichtungen gebildet; ein dritter Ausgang (`Output_Q56`) ist die ODER-Verknüpfung beider Richtungen. Die SubApp hat keine eigene Schnittstelle.

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `DigitalInput_CLK_I1` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I1`, `InputEvent` = `BUTTON_SINGLE_CLICK`, `QI` = `TRUE` | Liefert bei Einzelklick auf Taster I1 das Ereignis `IND`. |
| `DigitalInput_CLK_I2` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I2`, `InputEvent` = `BUTTON_PRESS_DOWN`, `QI` = `TRUE` | Liefert bei Niederdrücken von Taster I2 das Ereignis `IND`. |
| `DigitalInput_CLK_I3` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I3`, `InputEvent` = `BUTTON_PRESS_DOWN`, `QI` = `TRUE` | Liefert bei Niederdrücken von Taster I3 das Ereignis `IND`. |
| `E_DELAY` | [`iec61499::events::E_DELAY`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_DELAY/) | `DT` = `T#50ms` | Verzögert das Ereignis von I2 um 50 ms. |
| `E_SR_A` | [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/) | - | Set/Reset-Speicher für Richtung A (Ausgang `Q`). |
| `E_SR_B` | [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/) | - | Set/Reset-Speicher für Richtung B (Ausgang `Q`). |
| `AX_SPLIT_2_A` | [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) | - | Verteilt den Zustand von `E_SR_A` auf zwei Verbraucher. |
| `AX_SPLIT_2_B` | [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) | - | Verteilt den Zustand von `E_SR_B` auf zwei Verbraucher. |
| `AX_OR_2` | [`adapter::booleanOperators::AX_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | - | ODER-Verknüpfung der beiden Richtungen. |
| `DigitalOutput_Q5` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q5`, `QI` = `TRUE` | Ausgang für Richtung A. |
| `DigitalOutput_Q6` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q6`, `QI` = `TRUE` | Ausgang für Richtung B. |
| `DigitalOutput_Q56` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q56`, `QI` = `TRUE` | Gemeinsamer Ausgang: aktiv, wenn A oder B aktiv ist. |

## Programmablauf und Verbindungen

**Ereignisverbindungen:**

1. `DigitalInput_CLK_I1.IND` → `E_SR_A.S`: Einzelklick auf I1 setzt Richtung A.
2. `DigitalInput_CLK_I2.IND` → `E_SR_A.R`: Niederdrücken von I2 setzt Richtung A zurück.
3. `DigitalInput_CLK_I2.IND` → `E_DELAY.START` und `E_DELAY.EO` → `E_SR_B.S`: dasselbe Ereignis setzt nach 50 ms Richtung B.
4. `DigitalInput_CLK_I3.IND` → `E_SR_B.R`: Niederdrücken von I3 setzt Richtung B zurück.

**Adapterverbindungen:**

1. `E_SR_A.Q` → `AX_SPLIT_2_A.IN`; `AX_SPLIT_2_A.OUT1` → `DigitalOutput_Q5.OUT`; `AX_SPLIT_2_A.OUT2` → `AX_OR_2.IN1`.
2. `E_SR_B.Q` → `AX_SPLIT_2_B.IN`; `AX_SPLIT_2_B.OUT2` → `DigitalOutput_Q6.OUT`; `AX_SPLIT_2_B.OUT1` → `AX_OR_2.IN2`.
3. `AX_OR_2.OUT` → `DigitalOutput_Q56.OUT`.

Ein Zurücksetzen von Richtung B beim Setzen von A (I1) ist im Netzwerk nicht verschaltet. Beim Umschalten über I2 wird A zurückgesetzt und B erst 50 ms später gesetzt.

## Zusammenfassung

`ErsteAutomatisierung` zeigt mit drei Tastern, zwei Set/Reset-Speichern, einer 50-ms-Verzögerung und einer ODER-Verknüpfung, wie zwei Ausgänge für die Drehrichtungen eines Motors sowie ein gemeinsamer Ausgang aufgebaut werden.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
