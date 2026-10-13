# Maehwerksklappung: Mähwerksklappung (Ablaufkette)

![Maehwerksklappung_network](./Maehwerksklappung_network.svg)

* * * * * * * * * *

## Einleitung

`Maehwerksklappung` ist die Trainingsaufgabe „Mähwerksklappung" (Stand Februar 2026): Ein Mähwerk wird über einen Riegel (Entriegeln/Verriegeln) und einen Zylinder (Heben/Senken) zwischen den Zuständen **Arbeit** (unten) und **Transport** (oben) umgeklappt. Die Übung ist als **8-stufige Ablaufkette** mit `sequence_E_08_AX` aufgebaut; die Schritte werden durch Tastereignisse (`logiBUS_IE`) weitergeschaltet. Die Variante mit Pegel-Eingängen und reiner Boolescher Logik ist in `Maehwerksklappung_IXA` beschrieben.

Die Schnittstelle der SubApp ist leer, alle Ein- und Ausgänge sind direkt auf `Input_I1`…`Input_I6` und `Output_Q1`…`Output_Q6` gelegt.

## Verwendete Funktionsbausteine (FBs)

### Eingänge (Ereignisse)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Arbeit` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I1`, `BUTTON_SINGLE_CLICK` | Taster „Arbeit" (Einzelklick) |
| `S_1B1_riegel_offen` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I2`, `BUTTON_PRESS_DOWN` | Sensor 1B1: Riegel offen (Flanke beim Drücken) |
| `S_1B2_riegel_geschlossen` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I3`, `BUTTON_PRESS_DOWN` | Sensor 1B2: Riegel geschlossen (Flanke beim Drücken) |
| `S_1B2_riegel_geschlossen_1` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I3`, `BUTTON_PRESS_UP` | Derselbe Sensor 1B2, aber Flanke beim Loslassen |
| `S_2B1_M_unten` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I4`, `BUTTON_PRESS_DOWN` | Sensor 2B1: Mähwerk unten |
| `S_2B2_M_oben` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I5`, `BUTTON_PRESS_DOWN` | Sensor 2B2: Mähwerk oben |
| `Transport` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input_I6`, `BUTTON_SINGLE_CLICK` | Taster „Transport" (Einzelklick) |

### Ablaufsteuerung und Logik

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `sequence_E_08_AX` | [sequence_E_08_AX](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/utils/sequence/event/sequence_E_08_AX/) | – | Ereignisgesteuerte Schrittkette mit 8 Zuständen; jeder Zustand aktiviert einen Adapter-Ausgang `DO_S1`…`DO_S8` |
| `AX_OR_entriegeln` | [AX_OR_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | – | ODER aus `DO_S1` und `DO_S5` für den Ausgang „entriegeln" |
| `AX_OR_verriegeln` | [AX_OR_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | – | ODER aus `DO_S3` und `DO_S7` für den Ausgang „verriegeln" |
| `AX_OR_Lampe_gruen` | [AX_OR_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | – | ODER aus `DO_S4` und `DO_S8` für die grüne Lampe |
| `AX_BLINK` | [AX_BLINK](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/signals/AX_BLINK/) | `TIMELOW` = `T#1s`, `TIMEHIGH` = `T#500ms` | Blinkgeber für die orange Warnleuchte |

### Ausgänge

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Ausgang_heben` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q1` | Mähwerk heben |
| `Ausgang_senken` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q2` | Mähwerk senken |
| `Ausgang_verriegeln` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q3` | Riegel schließen |
| `Ausgang_entriegeln` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q4` | Riegel öffnen |
| `Lampe_gruen` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q5` | Grüne Lampe |
| `Blinker_orange` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q6` | Orange Blinkleuchte |

Alle Instanzen von `logiBUS_QXA`, `logiBUS_IE` setzen `QI` auf `TRUE`.

## Programmablauf und Verbindungen

Im Diagramm sind zwei Kommentare vermerkt: „S4 ist der Zustand ‚Arbeit'" und „S8 ist Zustand ‚Transport'". Die Schrittkette läuft wie folgt:

| Schritt | Weiterschaltung durch (Ereignis) | Aktiver Ausgang im Zustand |
|---|---|---|
| Start → S1 | `Arbeit.IND` (`START_S1`) | S1: `DO_S1` → entriegeln (Q4) |
| S1 → S2 | `S_1B1_riegel_offen.IND` | S2: `DO_S2` → senken (Q2) |
| S2 → S3 | `S_2B1_M_unten.IND` | S3: `DO_S3` → verriegeln (Q3) |
| S3 → S4 | `S_1B2_riegel_geschlossen.IND` | S4: `DO_S4` → grüne Lampe (Q5), Zustand „Arbeit" |
| S4 → S5 | `Transport.IND` | S5: `DO_S5` → entriegeln (Q4) |
| S5 → S6 | `S_1B1_riegel_offen.IND` | S6: `DO_S6` → heben (Q1) |
| S6 → S7 | `S_2B2_M_oben.IND` | S7: `DO_S7` → verriegeln (Q3) |
| S7 → S8 | `S_1B2_riegel_geschlossen.IND` | S8: `DO_S8` → grüne Lampe (Q5), Zustand „Transport" |
| S8 → Start | `Arbeit.IND` (`S8_START`) | – |

- Entriegeln (Q4) und Verriegeln (Q3) werden jeweils in zwei Zuständen benötigt und deshalb über `AX_OR_entriegeln` (S1, S5) und `AX_OR_verriegeln` (S3, S7) zusammengeführt. Dasselbe gilt für die grüne Lampe (S4, S8) über `AX_OR_Lampe_gruen`. Senken (S2) und Heben (S6) gehen direkt auf ihre Ausgänge.
- **Warnblinker**: Das Ereignis `S_1B2_riegel_geschlossen.IND` (Sensor 1B2 gedrückt) stoppt `AX_BLINK` (`STOP`), das Loslassen von 1B2 (`S_1B2_riegel_geschlossen_1.IND`) startet ihn (`START`). Der Blinkausgang `AX_BLINK.OUT` geht auf `Blinker_orange` (Q6). Damit blinkt die orange Leuchte, solange der Riegel nicht als geschlossen gemeldet wird.

## Zusammenfassung

`Maehwerksklappung` löst die Mähwerks-Aufgabe als klassische ereignisgesteuerte Schrittkette: Acht Zustände, durch Taster- und Sensorereignisse weitergeschaltet, steuern Riegel, Zylinder und Leuchten; ein separater Blinkgeber folgt allein dem Sensor 1B2.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
