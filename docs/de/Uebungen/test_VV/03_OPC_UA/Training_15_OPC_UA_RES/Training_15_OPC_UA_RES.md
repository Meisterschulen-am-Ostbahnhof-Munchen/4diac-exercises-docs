# Training_15_OPC_UA_RES: Methode AddValues über OPC-UA — zwei Geräte

![Training_15_OPC_UA_RES_network](./Training_15_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_15_OPC_UA_RES` setzt das Methoden-Beispiel `AddValues` aus `opcua.adoc` auf zwei Geräten um: Gerät B legt mit [`SERVER_1_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SERVER_1_2/) die OPC-UA-Methode `AddValues` an (zwei Eingangswerte, ein Ausgangswert) und berechnet die Summe mit [`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/). Gerät A ruft die Methode beim Drücken von `Input_I1` mit [`CLIENT_2_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_2_1/) mit den Werten 43 und 10 auf (laut SubApp-Kommentar Ergebnis 53).

Das Training ist das Zwei-Geräte-Gegenstück zu `L15` in `test_AX/Meins/OPC_UA_Lokal`.

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_15_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `ADD_CALL` ([`CLIENT_2_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_2_1/): `QI` = `TRUE`, `ID` = `T15_ADD_CALL`, `SD_1` = `INT#43`, `SD_2` = `INT#10`), `RESULT_TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_15_network.svg) | B (`192.168.1.12`) | `ADD_VALUES` ([`SERVER_1_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SERVER_1_2/): `QI` = `TRUE`, `ID` = `T15_ADD_METHOD`), `TO_DINT_1`, `TO_DINT_2` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/)), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten | Knoten | Bedeutung |
|---|---|---|
| `T15_ADD_METHOD / T15_ADD_CALL` | `/Objects/T15/AddValues` | Methode auf B; B legt sie lokal an (`CREATE_METHOD`), A ruft sie mit Endpunkt von B auf (`CALL_METHOD`) |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A), Ereignisverbindungen:

    1. `TASTER_I1.IND` → `ADD_CALL.REQ`
    2. `TASTER_I1.INITO` → `ADD_CALL.INIT`
    3. `ADD_CALL.CNF` → `RESULT_TO_DINT.REQ`

2. **`SubApp_PC_A`** (Gerät A), Datenverbindung:

    1. `ADD_CALL.RD_1` → `RESULT_TO_DINT.IN`

3. **`SubApp_PC_B`** (Gerät B), Ereignisverbindungen:

    1. `ADD_VALUES.IND` → `TO_DINT_1.REQ`
    2. `TO_DINT_1.CNF` → `TO_DINT_2.REQ`
    3. `TO_DINT_2.CNF` → `F_ADD.REQ`
    4. `F_ADD.CNF` → `TO_INT.REQ`
    5. `TO_INT.CNF` → `ADD_VALUES.RSP`

4. **`SubApp_PC_B`** (Gerät B), Datenverbindungen:

    1. `ADD_VALUES.RD_1` → `TO_DINT_1.IN`
    2. `ADD_VALUES.RD_2` → `TO_DINT_2.IN`
    3. `TO_DINT_1.OUT` → `F_ADD.IN1`
    4. `TO_DINT_2.OUT` → `F_ADD.IN2`
    5. `F_ADD.OUT` → `TO_INT.IN`
    6. `TO_INT.OUT` → `ADD_VALUES.SD_1`

## Technische Besonderheiten

- **Request/Response**: `SERVER_1_2` meldet den Aufruf mit `IND` und liefert das Ergebnis erst nach `RSP` zurück; die Rechenkette läuft dazwischen (`IND` → … → `RSP`).
- **Adresse ohne Knoten-ID**: der Knotenpfad `/Objects/T15/AddValues` enthält (anders als bei den Variablen-Knoten) keinen Zusatz `,1:s=...`.
- **Aufrufwerte**: `ADD_CALL` sendet die beim Baustein fest eingestellten Werte `SD_1` = `INT#43` und `SD_2` = `INT#10`.

## Hinweis

- **Nicht auf dem Gerät getestet.** `Training_13_OPC_UA_RES` bis `Training_18_OPC_UA_RES` wurden noch nicht auf einem Gerät ausgeführt. Diese Seite beschreibt nur, was in `test_Training_15_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` und `myOpcUaAddresses.gcf` steht; ob die Beispiele wie beschrieben laufen, ist nicht geprüft.
- Die Initialisierung kommt aus den Resources: In `EMB_RES_A` und `EMB_RES_B` führt `START.COLD` auf `E_TRIG` (`EVENTTYPE = 'EInit'`), das die Initialisierung (`EInit`) der Application auslöst. Deshalb hat der jeweils erste Baustein einer Kette in der SubApp keine eigene Verbindung auf seinen `INIT`-Eingang.
- Hinweis: Jeder `SERVER_0` braucht `IND` → `RSP` direkt am eigenen Baustein, sonst stocken bis zu 4 s lang alle OPC-UA-Publishes der Station (Regel siehe [SERVER_0](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SERVER_0/)). Bei Methoden mit Rückgabewert (`SERVER_1_2`) kommt `RSP` erst nach der sofortigen Berechnung.

## Lernziele

- Eine OPC-UA-Methode mit `SERVER_1_2` auf einem Gerät anlegen und mit `CLIENT_2_1` von einem anderen Gerät aufrufen.
- Zwei Eingangswerte und einen Ausgangswert einer Methode verdrahten.

## Zusammenfassung

`Training_15_OPC_UA_RES` ist das Methoden-Beispiel `AddValues` aus `opcua.adoc` auf zwei Geräten: B stellt die Methode bereit, A ruft sie per `Input_I1` mit 43 und 10 auf.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
