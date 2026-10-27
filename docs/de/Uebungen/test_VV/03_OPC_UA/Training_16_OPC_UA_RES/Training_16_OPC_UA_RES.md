# Training_16_OPC_UA_RES: Variablen per CLIENT lesen und schreiben — zwei Geräte

![Training_16_OPC_UA_RES_network](./Training_16_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_16_OPC_UA_RES` setzt das Beispiel „Variablen per CLIENT“ aus `opcua.adoc` auf zwei Geräten um: Beim Drücken von `Input_I1` schreibt Gerät A mit [`CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_1_0/) den Wert 42 auf den Knoten `Increment` von Gerät B und liest danach mit [`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/) den Knoten `Incremented` von Gerät B (laut SubApp-Kommentar 43). Gerät B abonniert `Increment` lokal, addiert 1 mit [`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/) und veröffentlicht das Ergebnis lokal auf `Incremented`.

Das Training ist das Zwei-Geräte-Gegenstück zu `L16` in `test_AX/Meins/OPC_UA_Lokal`.

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_16_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `WRITE_INC` ([`CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_1_0/): `QI` = `TRUE`, `ID` = `T16_INC_CLIENT_WRITE`, `SD_1` = `INT#42`), `READ_INCD` ([`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/): `QI` = `TRUE`, `ID` = `T16_INCD_CLIENT_READ`), `RESULT_TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_16_network.svg) | B (`192.168.1.12`) | `INCREMENT` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T16_INC_READ`), `TO_DINT` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/): `IN2` = `DINT#1`), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)), `INCREMENTED` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T16_INCD_WRITE`) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten | Knoten | Bedeutung |
|---|---|---|
| `T16_INC_CLIENT_WRITE / T16_INC_READ` | `/Objects/T16/Increment,1:s=T16_Increment` | Knoten auf B; A schreibt per `CLIENT` (`WRITE` mit Endpunkt von B), B liest lokal (`READ`) |
| `T16_INCD_WRITE / T16_INCD_CLIENT_READ` | `/Objects/T16/Incremented,1:s=T16_Incremented` | Knoten auf B; B schreibt lokal (`WRITE`), A liest per `CLIENT` (`READ` mit Endpunkt von B) |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A), Ereignisverbindungen:

    1. `TASTER_I1.IND` → `WRITE_INC.REQ`
    2. `WRITE_INC.CNF` → `READ_INCD.REQ`
    3. `TASTER_I1.INITO` → `WRITE_INC.INIT`
    4. `WRITE_INC.INITO` → `READ_INCD.INIT`
    5. `READ_INCD.CNF` → `RESULT_TO_DINT.REQ`

2. **`SubApp_PC_A`** (Gerät A), Datenverbindungen:

    1. `READ_INCD.RD_1` → `RESULT_TO_DINT.IN`

3. **`SubApp_PC_B`** (Gerät B), Ereignisverbindungen:

    1. `INCREMENT.IND` → `TO_DINT.REQ`
    2. `TO_DINT.CNF` → `F_ADD.REQ`
    3. `F_ADD.CNF` → `TO_INT.REQ`
    4. `TO_INT.CNF` → `INCREMENTED.REQ`
    5. `INCREMENT.INITO` → `INCREMENTED.INIT`

4. **`SubApp_PC_B`** (Gerät B), Datenverbindungen:

    1. `INCREMENT.RD_1` → `TO_DINT.IN`
    2. `TO_DINT.OUT` → `F_ADD.IN1`
    3. `F_ADD.OUT` → `TO_INT.IN`
    4. `TO_INT.OUT` → `INCREMENTED.SD_1`

## Technische Besonderheiten

- **Schreiben, dann Lesen**: `WRITE_INC.CNF` löst `READ_INCD.REQ` aus; das Lesen von `Incremented` beginnt erst nach der Bestätigung des Schreibens.
- **INIT-Kette auf A**: `TASTER_I1.INITO` → `WRITE_INC.INIT` → `READ_INCD.INIT`.
- **Typumwandlung**: auf B wird `INT` mit `F_INT_TO_DINT` auf `DINT` gewandelt, 1 addiert (`IN2` = `DINT#1`) und mit `F_DINT_TO_INT` zurückgewandelt; auf A wird das gelesene Ergebnis mit `F_INT_TO_DINT` auf `DINT` gewandelt.

## Hinweis

- **Nicht auf dem Gerät getestet.** `Training_13_OPC_UA_RES` bis `Training_18_OPC_UA_RES` wurden noch nicht auf einem Gerät ausgeführt. Diese Seite beschreibt nur, was in `test_Training_16_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` und `myOpcUaAddresses.gcf` steht; ob die Beispiele wie beschrieben laufen, ist nicht geprüft.
- Die Initialisierung kommt aus den Resources: In `EMB_RES_A` und `EMB_RES_B` führt `START.COLD` auf `E_TRIG` (`EVENTTYPE = 'EInit'`), das die Initialisierung (`EInit`) der Application auslöst. Deshalb hat der jeweils erste Baustein einer Kette in der SubApp keine eigene Verbindung auf seinen `INIT`-Eingang.

## Lernziele

- Knoten eines anderen Geräts mit `CLIENT_1_0` beschreiben und mit `CLIENT_0_1` lesen.
- Schreib- und Lesezugriff über `CNF` nacheinander ausführen.

## Zusammenfassung

`Training_16_OPC_UA_RES` ist das Beispiel „Variablen per CLIENT“ aus `opcua.adoc` auf zwei Geräten: A schreibt 42 auf `Increment` und liest `Incremented` von B, B addiert 1.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
