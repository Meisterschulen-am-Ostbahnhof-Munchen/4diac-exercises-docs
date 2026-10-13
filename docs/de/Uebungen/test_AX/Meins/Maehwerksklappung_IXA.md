# Maehwerksklappung_IXA: Mähwerksklappung (Boolesche Logik)

![Maehwerksklappung_IXA_network](./Maehwerksklappung_IXA_network.svg)

* * * * * * * * * *

## Einleitung

`Maehwerksklappung_IXA` löst dieselbe Aufgabe „Mähwerksklappung" (Stand Februar 2026) wie [`Maehwerksklappung`](./Maehwerksklappung.md), aber ohne Schrittkette: Die Eingänge werden als **Pegel** (`logiBUS_IXA`) gelesen und die Ausgänge ausschließlich durch Boolesche Verknüpfungen (UND/ODER/NICHT) über Adapter gebildet. Die Logikgleichungen stehen als Kommentare im Netzwerk (Y_1 … Y_4, H_1, H_2).

Die Schnittstelle der SubApp ist leer; Eingänge `Input_I1`…`Input_I6`, Ausgänge `Output_Q1`…`Output_Q6`.

## Verwendete Funktionsbausteine (FBs)

### Eingänge

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Arbeit` | [logiBUS_IXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input_I1` | Taster „Arbeit" |
| `S_1B1_riegel_offen` | [logiBUS_IXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input_I2` | Sensor 1B1: Riegel offen |
| `S_1B2_riegel_geschlossen` | [logiBUS_IXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input_I3` | Sensor 1B2: Riegel geschlossen |
| `S_2B1_M_unten` | [logiBUS_IXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input_I4` | Sensor 2B1: Mähwerk unten |
| `S_2B2_M_oben` | [logiBUS_IXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input_I5` | Sensor 2B2: Mähwerk oben |
| `Transport` | [logiBUS_IXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input_I6` | Taster „Transport" |
| `AX_TON_Arbeit`, `AX_TON_Transport` | [AX_TON](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/timers/AX_TON/) | `PT` = `T#2s` | Einschaltverzögerung 2 s für die Taster „Arbeit" bzw. „Transport" |

### Logik

| Instanz | Typ | Erklärung |
|---|---|---|
| `riegel_offen2` | [AX_SPLIT_3](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_3/) | Verteilt 1B1 auf drei Verbraucher (Entriegeln, Heben, Senken) |
| `Arbeit2`, `Transport2`, `oben2`, `unten2`, `riegel_geschlossen2`, `oben_oder_unten2`, `NOT_riegel_geschlossen2` | [AX_SPLIT_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) | Verteilen ein Signal auf zwei Verbraucher |
| `NOT_riegel_offen`, `NOT_oben`, `NOT_unten`, `NOT_riegel_geschlossen` | [AX_NOT](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_NOT/) | Negation von 1B1, 2B2, 2B1 bzw. 1B2 |
| `Arbeit_oder_Transport`, `oben_oder_unten` | [AX_OR_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | ODER aus „Arbeit"/„Transport" bzw. „oben"/„unten" |
| `entriegeln`, `verriegeln`, `Endlage_verriegelt` | [AX_AND_2](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_AND_2/) | UND-Verknüpfung für Entriegeln, Verriegeln und Statuslampe |
| `heben`, `senken` | [AX_AND_3](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_AND_3/) | UND-Verknüpfung mit drei Eingängen für Heben und Senken |

### Ausgänge

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Ausgang_heben` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q1` | Mähwerk heben |
| `Ausgang_senken` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q2` | Mähwerk senken |
| `Ausgang_verriegeln` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q3` | Riegel schließen |
| `Ausgang_entriegeln` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q4` | Riegel öffnen |
| `Lampe_gruen` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q5` | Statusleuchte H_2 |
| `Blinker_orange` | [logiBUS_QXA](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output_Q6` | Warnleuchte H_1 |

## Programmablauf und Verbindungen

Die Logik entspricht den Kommentaren im Netzwerk (S_1 = „Transport", S_2 = „Arbeit"; 1B1 Riegel offen, 1B2 Riegel geschlossen, 2B1 unten, 2B2 oben):

1. **Entriegeln** (Q4): `Y_1 = (S_1 oder S_2) und nicht 1B1`. Hat der Fahrer „Arbeit" oder „Transport" gedrückt und der Riegel ist noch nicht offen, wird der Riegel geöffnet (`Arbeit_oder_Transport` → `entriegeln` mit `NOT_riegel_offen`).
2. **Heben** (Q1): `Y_3 = S_1 und 1B1 und nicht 2B2`. Aktiv, solange „Transport" gehalten wird, der Riegel offen ist und die obere Endlage noch nicht erreicht ist (`heben`). Beim Loslassen fällt der Ausgang ab, das Ventil stoppt den Zylinder.
3. **Senken** (Q2): `Y_4 = S_2 und 1B1 und nicht 2B1`. Analog zum Heben mit „Arbeit" und der unteren Endlage (`senken`).
4. **Verriegeln** (Q3): `Y_2 = (2B2 oder 2B1) und nicht 1B2`. Sobald das Mähwerk in einer Endlage ist und der Riegel noch nicht geschlossen ist, wird verriegelt (`oben_oder_unten` → `verriegeln`).
5. **Warnleuchte** (Q6): `H_1 = nicht 1B2`. Leuchtet, solange der Riegel nicht komplett geschlossen ist (`NOT_riegel_geschlossen` → `Blinker_orange`).
6. **Statusleuchte** (Q5): `H_2 = 1B2 und (2B2 oder 2B1)`. Signalisiert den sicheren Zustand „verriegelt in Endlage" (`Endlage_verriegelt` → `Lampe_gruen`).

Die Taster „Arbeit" und „Transport" laufen jeweils über eine 2-s-Einschaltverzögerung (`AX_TON_Arbeit`, `AX_TON_Transport`), bevor sie in die Gleichungen für Entriegeln, Heben und Senken eingehen; in den Kommentaren des Netzwerks ist diese Verzögerung nicht erwähnt.

## Zusammenfassung

`Maehwerksklappung_IXA` setzt die Mähwerks-Aufgabe ohne Zustandsspeicher als reine Kombinatorik um: Sechs Eingänge werden mit Splittern verteilt, mit UND/ODER/NICHT verknüpft und steuern Riegel, Zylinder und beide Leuchten direkt aus dem aktuellen Sensorbild.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
