# Training_13_OPC_UA_RES: Flip-Flop über OPC-UA — zwei Geräte

![Training_13_OPC_UA_RES_network](./Training_13_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_13_OPC_UA_RES` setzt das Flip-Flop-Beispiel aus `opcua.adoc` auf zwei Geräten um: Gerät A schreibt den Zustand des physischen Eingangs `Input_I1` per OPC-UA auf den Knoten `Flip` von Gerät B und zeigt den Knoten `Flop` von Gerät B am physischen Ausgang `Output_Q1` an. Gerät B abonniert `Flip` lokal, schaltet den Wert über [`E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_SWITCH/) auf den Set- bzw. Reset-Eingang von [`E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_SR/) und veröffentlicht den Zustand von `E_SR` lokal auf `Flop`. Laut SubApp-Kommentar zeigt `Output_Q1` damit `Flop` (= NICHT `Input_I1`).

Das Training ist das Zwei-Geräte-Gegenstück zu `L13` in `test_AX/Meins/OPC_UA_Lokal` (dort läuft dasselbe Beispiel auf einem Gerät).

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_13_network.svg) | A (`192.168.1.11`) | Zwei Composites, die das OPC-UA-Protokoll selbst enthalten: `INPUT_I1` ([`logiBUS_IXA_TO_CLIENT_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_CLIENT_OPC/): `Input` = `Input_I1`, `ID` = `T13_FLIP_REMOTE_WRITE`) schreibt den Eingang auf `Flip` von B; `OUTPUT_Q1` ([`logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/): `Output` = `Output_Q1`, `ID` = `T13_FLOP_REMOTE_SUBSCRIBE`) abonniert `Flop` von B und schaltet damit den Ausgang |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_13_network.svg) | B (`192.168.1.12`) | `FLIP` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T13_FLIP_READ`), `E_SWITCH` ([`E_SWITCH`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_SWITCH/)), `E_SR` ([`E_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_SR/)), `FLOP` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T13_FLOP_WRITE`) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten | Knoten | Bedeutung |
|---|---|---|
| `T13_FLIP_REMOTE_WRITE / T13_FLIP_READ` | `/Objects/T13/Flip,1:s=T13_Flip` | Knoten auf B; A schreibt (`WRITE` mit Endpunkt von B), B liest lokal (`READ`) |
| `T13_FLOP_WRITE / T13_FLOP_REMOTE_SUBSCRIBE` | `/Objects/T13/Flop,1:s=T13_Flop` | Knoten auf B; B schreibt lokal (`WRITE`), A abonniert (`SUBSCRIBE` mit Endpunkt von B) |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A): keine Verbindungen in der SubApp; `INPUT_I1` und `OUTPUT_Q1` sind unabhängig und koppeln nur über OPC-UA.

2. **`SubApp_PC_B`** (Gerät B), Ereignisverbindungen:

    1. `FLIP.IND` → `E_SWITCH.EI`
    2. `E_SWITCH.EO0` → `E_SR.S`
    3. `E_SWITCH.EO1` → `E_SR.R`
    4. `E_SR.EO` → `FLOP.REQ`
    5. `FLIP.INITO` → `FLOP.INIT`

3. **`SubApp_PC_B`** (Gerät B), Datenverbindungen:

    1. `FLIP.RD_1` → `E_SWITCH.G`
    2. `E_SR.Q` → `FLOP.SD_1`

## Technische Besonderheiten

- **Lokal und remote**: Gerät B greift auf seine eigenen Knoten lokal zu (`F_OPC_LOCAL`: `READ` für `FLIP`, `WRITE` für `FLOP`), Gerät A greift mit dem Endpunkt von B (`PC_B_OPC`) zu (`F_OPC_REMOTE`: `WRITE` bzw. `SUBSCRIBE`).
- **Flip-Flop**: `E_SWITCH` schaltet je nach Wert von `FLIP` (`G`) den Ausgang `EO0` (auf `E_SR.S`) oder `EO1` (auf `E_SR.R`); `E_SR.Q` wird auf `Flop` veröffentlicht.

## Hinweis

- **Nicht auf dem Gerät getestet.** `Training_13_OPC_UA_RES` bis `Training_18_OPC_UA_RES` wurden noch nicht auf einem Gerät ausgeführt. Diese Seite beschreibt nur, was in `test_Training_13_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` und `myOpcUaAddresses.gcf` steht; ob die Beispiele wie beschrieben laufen, ist nicht geprüft.
- Die Initialisierung kommt aus den Resources: In `EMB_RES_A` und `EMB_RES_B` führt `START.COLD` auf `E_TRIG` (`EVENTTYPE = 'EInit'`), das die Initialisierung (`EInit`) der Application auslöst. Deshalb hat der jeweils erste Baustein einer Kette in der SubApp keine eigene Verbindung auf seinen `INIT`-Eingang.

## Lernziele

- Das Flip-Flop-Beispiel aus `opcua.adoc` auf zwei Geräten aufbauen.
- Knoten per `PUBLISH`/`SUBSCRIBE` lokal bedienen und von einem anderen Gerät mit Endpunkt beschreiben bzw. abonnieren.

## Zusammenfassung

`Training_13_OPC_UA_RES` ist das Flip-Flop aus `opcua.adoc` auf zwei Geräten: A schreibt `Input_I1` auf `Flip` und zeigt `Flop` an `Output_Q1`; B bildet `Flop` aus `Flip` mit `E_SWITCH` und `E_SR`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
