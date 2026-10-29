# Uebung_OPC_L10_AX: Hoch/Runter-Taster mit Interlock lokal per PUBLISH/SUBSCRIBE

![Uebung_OPC_L10_AX_network](./Uebung_OPC_L10_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L10_AX` ist die lokale Variante von `Training_10_OPC_UA_RES`: Die Taster `Input_I1` (Hoch) und `Input_I2` (Runter) werden per lokalem `PUBLISH` auf OPC-UA-Knoten geschrieben, per lokalem `SUBSCRIBE` wieder gelesen und über einen Interlock mit Schutzzeit (`ILOCK_SWITCH_PROTECT_AX`, `DT_PROTECT` = `T#1s`) auf `Output_Q7` und `Output_Q8` geschaltet. Beide Hälften der Vorlage laufen auf **einem** Gerät; es gibt keinen Methodenaufruf, also keinen Endpunkt.

Übersicht aller lokalen Übungen: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Verwendete Funktionsbausteine (FBs)

| Name | Typ | Parameter | Erklärung |
|---|---|---|---|
| `UpTaster` | [`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/) | `Input` = `Input_I1`, `ID` = `SW1_UP_WRITE` | Liest `Input_I1` und schreibt den Wert lokal per `PUBLISH` (Aktion `WRITE`, ohne Endpunkt) auf den Hoch-Knoten. |
| `DownTaster` | [`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/) | `Input` = `Input_I2`, `ID` = `SW1_DOWN_WRITE` | Wie `UpTaster`, für `Input_I2` und den Runter-Knoten. |
| `SUBSCRIBE_UP` | [`adapter::net::AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SW1_UP_READ` | Liest den Hoch-Knoten lokal per `SUBSCRIBE` und gibt den Wert über einen `AX`-Adapter aus. |
| `SUBSCRIBE_DOWN` | [`adapter::net::AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SW1_DOWN_READ` | Wie `SUBSCRIBE_UP`, für den Runter-Knoten. |
| `ILOCK_AX` | [`logiBUS::signalprocessing::interlock::ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/) | `DT_PROTECT` = `T#1s` | Interlock Hoch/Runter mit Schutz-Totzeit vor einem Richtungswechsel; Ausgänge `UP_OUT`/`DOWN_OUT`, Zeitüberwachung über den Socket `timeOut`. |
| `E_TimeOut` | [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/) | - | Zeitgeber für die Schutz-Totzeit, über `TimeOutSocket` mit `ILOCK_AX.timeOut` verbunden. |
| `DigitalOutput_Q7` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q7` | Physischer Ausgang für Hoch. |
| `DigitalOutput_Q8` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q8` | Physischer Ausgang für Runter. |

## OPC-UA-Adressraum

Die IDs stammen aus `const\OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`) und werden mit den Functions der Lib `MyLib_OPC` berechnet (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Konstante | Aktion | Knoten / Methode | Bedeutung |
|---|---|---|---|
| `SW1_UP_WRITE` | `WRITE` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Hoch-Taster lokal schreiben (PUBLISH) |
| `SW1_UP_READ` | `READ` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Hoch-Taster lokal lesen (SUBSCRIBE) |
| `SW1_DOWN_WRITE` | `WRITE` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Runter-Taster lokal schreiben (PUBLISH) |
| `SW1_DOWN_READ` | `READ` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Runter-Taster lokal lesen (SUBSCRIBE) |

## Programmablauf und Verbindungen

Die SubApps `UpTaster` und `DownTaster` sind nicht verdrahtet; ihr Wert erreicht `SUBSCRIBE_UP`/`SUBSCRIBE_DOWN` nur über die OPC-UA-Knoten. Adapterverbindungen:

1. `SUBSCRIBE_UP.OUT` → `ILOCK_AX.UP_IN`
2. `SUBSCRIBE_DOWN.OUT` → `ILOCK_AX.DOWN_IN`
3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`
4. `ILOCK_AX.UP_OUT` → `DigitalOutput_Q7.OUT`
5. `ILOCK_AX.DOWN_OUT` → `DigitalOutput_Q8.OUT`

## Technische Besonderheiten

- **Lokaler Roundtrip ohne Endpunkt**: `WRITE` und `READ` auf demselben Knoten, beide ohne Endpunkt; kein `CALL_METHOD` nötig.
- **Schutz-Totzeit**: `DT_PROTECT` = `T#1s` begrenzt den Richtungswechsel im Interlock.

## Lernziele

- Taster lokal per `PUBLISH` auf OPC-UA-Knoten schreiben und per `SUBSCRIBE` wieder lesen.
- Interlock mit Schutz-Totzeit (`ILOCK_SWITCH_PROTECT_AX`) und `E_TimeOut` einsetzen.
- Zwei Ausgänge (`Output_Q7`, `Output_Q8`) gegeneinander verriegeln.

**Schwierigkeitsgrad**: Fortgeschritten  
**Empfohlene Reihenfolge**: `Training_10_OPC_UA_RES` (Vorlage, zwei Geräte), `Uebung_OPC_L01_AX`

## Hinweis / Ergebnis

- **Nicht auf dem Gerät getestet.** Auf `FORTE_PC_AX` getestet sind bisher nur L01, L03 und L19; `Uebung_OPC_L10_AX` wurde noch nicht auf dem Gerät ausgeführt.
- Lokal (ohne Endpunkt) laufen `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` und `PUBLISH_0`. `CALL_METHOD` und die Client-Subscription laufen dagegen mit Endpunkt an die eigene Station (`localhost` oder eigene IP; beides zählt als remote).

## Zusammenfassung

`Uebung_OPC_L10_AX` führt Hoch/Runter-Taster über lokale OPC-UA-Knoten (`PUBLISH` und `SUBSCRIBE`) in einen Interlock mit Schutzzeit, der `Output_Q7`/`Output_Q8` schaltet.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
