# Uebung_OPC_L11_AX: Hoch/Runter-SoftKeys mit Interlock und Zustandsrückmeldung lokal

![Uebung_OPC_L11_AX_network](./Uebung_OPC_L11_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L11_AX` ist die lokale Variante von `Training_11_OPC_UA_RES`: Statt der Taster werden die SoftKeys `SoftKey_F1` (Hoch) und `SoftKey_F2` (Runter) verwendet. Ihre Betätigung geht lokal per `PUBLISH` auf OPC-UA-Knoten, wird per `SUBSCRIBE` gelesen und durch den Interlock (`ILOCK_SWITCH_PROTECT_AX`) auf `Output_Q7`/`Output_Q8` geschaltet. Der erreichte Zustand wird lokal per `PUBLISH` geschrieben, per `SUBSCRIBE` gelesen und färbt den SoftKey-Hintergrund. Alles läuft auf **einem** Gerät ohne Endpunkt.

Übersicht aller lokalen Übungen: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Verwendete Funktionsbausteine (FBs)

| Name | Typ | Parameter | Erklärung |
|---|---|---|---|
| `UpKey` | [`isobus::UT::io::Softkey::Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/) | `QI` = `TRUE`, `u16ObjId` = `SoftKey_F1` | SoftKey Hoch; der `IN`-Adapter liefert die Betätigung. |
| `DownKey` | [`isobus::UT::io::Softkey::Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/) | `QI` = `TRUE`, `u16ObjId` = `SoftKey_F2` | SoftKey Runter. |
| `UpTasterClient` | [`adapter::net::AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) | `QI` = `TRUE`, `ID` = `SW1_UP_WRITE` | Schreibt die Hoch-Betätigung lokal per `PUBLISH`. |
| `DownTasterClient` | [`adapter::net::AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) | `QI` = `TRUE`, `ID` = `SW1_DOWN_WRITE` | Schreibt die Runter-Betätigung lokal per `PUBLISH`. |
| `StateUpSubscribe` | [`adapter::net::AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SW1_UP_STATE_READ` | Liest den Zustand „Hoch erreicht“ lokal per `SUBSCRIBE`. |
| `StateDownSubscribe` | [`adapter::net::AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SW1_DOWN_STATE_READ` | Liest den Zustand „Runter erreicht“ lokal per `SUBSCRIBE`. |
| `BG_Up` | [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) | `u16ObjId` = `SoftKey_F1` | Hintergrundfarbe des Hoch-SoftKeys (TRUE = grün, FALSE = weiß). |
| `BG_Down` | [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) | `u16ObjId` = `SoftKey_F2` | Hintergrundfarbe des Runter-SoftKeys. |
| `SUBSCRIBE_UP` | [`adapter::net::AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SW1_UP_READ` | Liest die Hoch-Betätigung lokal per `SUBSCRIBE`. |
| `SUBSCRIBE_DOWN` | [`adapter::net::AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) | `QI` = `TRUE`, `ID` = `SW1_DOWN_READ` | Liest die Runter-Betätigung lokal per `SUBSCRIBE`. |
| `ILOCK_AX` | [`logiBUS::signalprocessing::interlock::ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/) | `DT_PROTECT` = `T#1s` | Interlock Hoch/Runter mit Schutz-Totzeit. |
| `E_TimeOut` | [`iec61499::events::E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/) | - | Zeitgeber für die Schutz-Totzeit (`ILOCK_AX.timeOut`). |
| `SPLIT_UP` | [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) | - | Verteilt den interlockten Hoch-Zustand auf Ausgang und Rückmeldung. |
| `SPLIT_DOWN` | [`adapter::events::unidirectional::AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) | - | Verteilt den interlockten Runter-Zustand auf Ausgang und Rückmeldung. |
| `DigitalOutput_Q7` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q7` | Physischer Ausgang für Hoch. |
| `DigitalOutput_Q8` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `QI` = `TRUE`, `Output` = `Output_Q8` | Physischer Ausgang für Runter. |
| `StateUpClient` | [`adapter::net::AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) | `QI` = `TRUE`, `ID` = `SW1_UP_STATE_WRITE` | Schreibt den erreichten Hoch-Zustand lokal per `PUBLISH`. |
| `StateDownClient` | [`adapter::net::AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) | `QI` = `TRUE`, `ID` = `SW1_DOWN_STATE_WRITE` | Schreibt den erreichten Runter-Zustand lokal per `PUBLISH`. |

## OPC-UA-Adressraum

Die IDs stammen aus `const\OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`) und werden mit den Functions der Lib `MyLib_OPC` berechnet (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Konstante | Aktion | Knoten / Methode | Bedeutung |
|---|---|---|---|
| `SW1_UP_WRITE` / `SW1_UP_READ` | `WRITE` / `READ` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Hoch-Betätigung (PUBLISH / SUBSCRIBE) |
| `SW1_DOWN_WRITE` / `SW1_DOWN_READ` | `WRITE` / `READ` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Runter-Betätigung (PUBLISH / SUBSCRIBE) |
| `SW1_UP_STATE_WRITE` / `SW1_UP_STATE_READ` | `WRITE` / `READ` | `/Objects/Switch1/UpState,1:s=Switch1_UpState` | Hoch erreicht (PUBLISH / SUBSCRIBE) |
| `SW1_DOWN_STATE_WRITE` / `SW1_DOWN_STATE_READ` | `WRITE` / `READ` | `/Objects/Switch1/DownState,1:s=Switch1_DownState` | Runter erreicht (PUBLISH / SUBSCRIBE) |

## Programmablauf und Verbindungen

Adapterverbindungen:

1. `UpKey.IN` → `UpTasterClient.IN`
2. `DownKey.IN` → `DownTasterClient.IN`
3. `StateUpSubscribe.OUT` → `BG_Up.DI1`
4. `StateDownSubscribe.OUT` → `BG_Down.DI1`
5. `SUBSCRIBE_UP.OUT` → `ILOCK_AX.UP_IN`
6. `SUBSCRIBE_DOWN.OUT` → `ILOCK_AX.DOWN_IN`
7. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`
8. `ILOCK_AX.UP_OUT` → `SPLIT_UP.IN`
9. `SPLIT_UP.OUT1` → `DigitalOutput_Q7.OUT`
10. `SPLIT_UP.OUT2` → `StateUpClient.IN`
11. `ILOCK_AX.DOWN_OUT` → `SPLIT_DOWN.IN`
12. `SPLIT_DOWN.OUT1` → `DigitalOutput_Q8.OUT`
13. `SPLIT_DOWN.OUT2` → `StateDownClient.IN`

## Technische Besonderheiten

- **Lokaler Roundtrip ohne Endpunkt**: Alle vier Knotenpaare werden lokal mit `WRITE`/`READ` bedient; kein `CALL_METHOD`.
- **Rückmeldung nach dem Interlock**: `SPLIT_UP`/`SPLIT_DOWN` schreiben den tatsächlich geschalteten Zustand zurück; der SoftKey-Hintergrund zeigt damit den Ausgangszustand nach dem Interlock.

## Lernziele

- SoftKeys statt Taster als Quelle verwenden und lokal über OPC-UA-Knoten führen.
- Den Zustand nach dem Interlock mit `AX_SPLIT_2` auf Ausgang und Rückmeldung verteilen.
- Rückmeldung per `SUBSCRIBE` auf den SoftKey-Hintergrund (`GreenWhiteBackground1_AX`) führen.

**Schwierigkeitsgrad**: Fortgeschritten  
**Empfohlene Reihenfolge**: `Uebung_OPC_L10_AX`, `Training_11_OPC_UA_RES` (Vorlage, zwei Geräte)

## Hinweis / Ergebnis

- **Nicht auf dem Gerät getestet.** Auf `FORTE_PC_AX` getestet sind bisher nur L01, L03 und L19; `Uebung_OPC_L11_AX` wurde noch nicht auf dem Gerät ausgeführt.
- Lokal (ohne Endpunkt) laufen `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` und `PUBLISH_0`. `CALL_METHOD` und die Client-Subscription laufen dagegen mit Endpunkt an die eigene Station (`localhost` oder eigene IP; beides zählt als remote).

## Zusammenfassung

`Uebung_OPC_L11_AX` ersetzt die Taster aus L10 durch SoftKeys und meldet den erreichten Zustand lokal per `PUBLISH`/`SUBSCRIBE` an den SoftKey-Hintergrund zurück.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
