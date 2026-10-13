# ZylinderAusEinAutomatik: Zylinder aus/ein per Taster

![ZylinderAusEinAutomatik_network](./ZylinderAusEinAutomatik_network.svg)

* * * * * * * * * *

## Einleitung

`ZylinderAusEinAutomatik` steuert einen Zylinder, der über zwei Ausgänge (`Output_Q5` = ausfahren, `Output_Q6` = einfahren) und einen gemeinsamen Masse-Ausgang (`Output_Q56`) angeschlossen ist. Drei Taster schalten die Bewegung: Einer startet das Ausfahren, einer stoppt es und startet nach kurzer Verzögerung das Einfahren, einer beendet das Einfahren. Die Schnittstelle der SubApp ist leer. (Der `Comment` der SubApp lautet noch „Motor Links/Rechtslauf" und stammt offenbar von einer Vorlage.)

Ein Kommentar im Netzwerk vermerkt: „Der Zylinder macht beim Ausfahren ein summendes Geräusch."

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `DigitalInput_CLK_I1` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I1`, `BUTTON_SINGLE_CLICK`, `QI` = `TRUE` | Taster „Ausfahren" (Einzelklick) |
| `DigitalInput_CLK_I2` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I2`, `BUTTON_PRESS_DOWN`, `QI` = `TRUE` | Taster „Einfahren starten" (beim Drücken) |
| `DigitalInput_CLK_I3` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I3`, `BUTTON_PRESS_DOWN`, `QI` = `TRUE` | Taster „Einfahren beenden" (beim Drücken) |
| `AX_SR_1` | [AX_SR](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/) | – | Speicher (Set/Reset) für „Zylinder ausfahren" |
| `AX_SR_2` | [AX_SR](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/) | – | Speicher (Set/Reset) für „Zylinder einfahren" |
| `E_DELAY` | [E_DELAY](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_DELAY/) | `DT` = `T#50ms` | Verzögert den Start des Einfahrens um 50 ms |
| `AX_SPLIT_2_A`, `AX_SPLIT_2_B` | [AX_SPLIT_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) | – | Verteilen jeweils einen Speicherzustand auf den Zylinderausgang und das ODER |
| `AX_OR_2` | [AX_OR_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | – | ODER der beiden Bewegungen für den gemeinsamen Ausgang |
| `Zylinder_aus` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q5`, `QI` = `TRUE` | Zylinder ausfahren |
| `Zylinder_ein` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q6`, `QI` = `TRUE` | Zylinder einfahren |
| `Gnd` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q56`, `QI` = `TRUE` | Gemeinsamer Ausgang, aktiv wenn eine der beiden Bewegungen läuft |

## Programmablauf und Verbindungen

1. **Ausfahren starten**: `DigitalInput_CLK_I1.IND` setzt `AX_SR_1` (`S`). `AX_SR_1.Q` geht über `AX_SPLIT_2_A` auf `Zylinder_aus` (Q5) und auf `AX_OR_2.IN1`.
2. **Ausfahren stoppen, Einfahren starten**: `DigitalInput_CLK_I2.IND` setzt `AX_SR_1` zurück (`R`) und startet `E_DELAY`. Nach 50 ms setzt `E_DELAY.EO` den Speicher `AX_SR_2` (`S`). `AX_SR_2.Q` geht über `AX_SPLIT_2_B` auf `Zylinder_ein` (Q6) und auf `AX_OR_2.IN2`.
3. **Einfahren beenden**: `DigitalInput_CLK_I3.IND` setzt `AX_SR_2` zurück (`R`).
4. **Gemeinsamer Ausgang**: `AX_OR_2.OUT` steuert `Gnd` (Q56) an; dieser ist aktiv, sobald entweder Ausfahren oder Einfahren aktiv ist.

Die 50 ms Verzögerung (`E_DELAY`) liegt zwischen dem Zurücksetzen von `AX_SR_1` und dem Setzen von `AX_SR_2`.

## Zusammenfassung

`ZylinderAusEinAutomatik` zeigt eine Zylindersteuerung mit zwei Set/Reset-Speichern, einer kurzen Umschaltverzögerung und einem gemeinsam angesteuerten Masse-Ausgang.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
