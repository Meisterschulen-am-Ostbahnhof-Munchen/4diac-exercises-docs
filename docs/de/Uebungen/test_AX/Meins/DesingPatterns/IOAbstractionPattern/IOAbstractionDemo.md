# IOAbstractionDemo: IO-Abstraktionsschicht-Muster

![IOAbstractionDemo_network](./IOAbstractionDemo_network.svg)

* * * * * * * * * *

## Einleitung

`IOAbstractionDemo` zeigt das IO-Abstraktionsschicht-Muster: `logiBUS_IE` und `logiBUS_QX` bilden die Hardware- bzw. HAL-Schicht um die vollständig ereignisgesteuerte Anwendungsschicht `EventDrivenCylinder` (Kommentar im Typ). Der Zylinder wird über vier Eingänge (Ausfahren-/Einfahren-Anforderung, Endlagen Home/Ende) angesteuert und schaltet zwei Ausgänge (Ausfahren, Einfahren). Die SubApp hat keine eigene Schnittstelle.

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `DigitalInput_ExtendReq` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I1`, `InputEvent` = `BUTTON_SINGLE_CLICK`, `QI` = `TRUE` | Anforderung Ausfahren. |
| `DigitalInput_RetractReq` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I2`, `InputEvent` = `BUTTON_SINGLE_CLICK`, `QI` = `TRUE` | Anforderung Einfahren. |
| `DigitalInput_AtHome` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I3`, `InputEvent` = `BUTTON_SINGLE_CLICK`, `QI` = `TRUE` | Meldung Endlage Home. |
| `DigitalInput_AtEnd` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I4`, `InputEvent` = `BUTTON_SINGLE_CLICK`, `QI` = `TRUE` | Meldung Endlage Ende. |
| `App` | `Meins::DesingPatterns::PurelyEventDrivenPattern::EventDrivenCylinder` | - | Ereignisgesteuerter Zylinder ohne BOOL-Datenpins (Zustände HOME, EXTENDING, EXTENDED, RETRACTING usw.). |
| `ExtendLatch` | [`iec61499::events::E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_SR/) | - | Speicher für die Ausfahren-Ansteuerung. |
| `RetractLatch` | [`iec61499::events::E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_SR/) | - | Speicher für die Einfahren-Ansteuerung. |
| `DigitalOutput_Extend` | [`logiBUS::io::DQ::logiBUS_QX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QX/) | `Output` = `Output_Q1`, `QI` = `TRUE` | Ausgang Ausfahren. |
| `DigitalOutput_Retract` | [`logiBUS::io::DQ::logiBUS_QX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QX/) | `Output` = `Output_Q2`, `QI` = `TRUE` | Ausgang Einfahren. |

## Programmablauf und Verbindungen

**Eingangsseite (Hardware → Anwendung):**

1. `DigitalInput_ExtendReq.INITO` → `App.START` (Kommentar: "Bootstrap: fires once at deployment").
2. `DigitalInput_ExtendReq.IND` → `App.EXTEND_REQ`; `DigitalInput_RetractReq.IND` → `App.RETRACT_REQ`.
3. `DigitalInput_AtHome.IND` → `App.AT_HOME`; `DigitalInput_AtEnd.IND` → `App.AT_END`.

**Ausgangsseite (Anwendung → Hardware):**

1. `App.EXTEND` → `ExtendLatch.S` und `RetractLatch.R`.
2. `App.RETRACT` → `RetractLatch.S` und `ExtendLatch.R`.
3. `ExtendLatch.EO` → `DigitalOutput_Extend.REQ`, `ExtendLatch.Q` → `DigitalOutput_Extend.OUT`.
4. `RetractLatch.EO` → `DigitalOutput_Retract.REQ`, `RetractLatch.Q` → `DigitalOutput_Retract.OUT`.

Die Anwendung kennt nur Ereignisse; die Umsetzung auf Pegel übernehmen die beiden E_SR-Speicher und die `logiBUS_QX`-Ausgänge. `EventDrivenCylinder` ist ein Basis-FB im Projektordner `DesingPatterns/PurelyEventDrivenPattern`.

## Zusammenfassung

Das Demo trennt Hardwarezugriff (logiBUS-Ein-/Ausgänge, Speicher) von der Anwendungslogik (`EventDrivenCylinder`), die ausschließlich mit Ereignissen arbeitet.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
