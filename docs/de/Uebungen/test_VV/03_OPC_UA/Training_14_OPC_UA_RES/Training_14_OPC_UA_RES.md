# Training_14_OPC_UA_RES: Adder über OPC-UA — zwei Geräte

![Training_14_OPC_UA_RES_network](./Training_14_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_14_OPC_UA_RES` setzt das Adder-Beispiel aus `opcua.adoc` auf Gerät B um: B abonniert die lokalen Knoten `Num1` und `Num2`, addiert die beiden Werte mit [`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/) und veröffentlicht die Summe lokal auf dem Knoten `Result`. Die Werte werden laut SubApp-Kommentar z. B. per UaExpert gesetzt. Die SubApp von Gerät A ist leer.

Das Training ist das Zwei-Geräte-Gegenstück zu `L14` in `test_AX/Meins/OPC_UA_Lokal`.

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_14_network.svg) | A (`192.168.1.11`) | leer (keine Bausteine, keine Verbindungen) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_14_network.svg) | B (`192.168.1.12`) | `NUM1` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T14_NUM1_READ`), `NUM2` ([`SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SUBSCRIBE_1/): `QI` = `TRUE`, `ID` = `T14_NUM2_READ`), `TO_DINT_1`, `TO_DINT_2` ([`F_INT_TO_DINT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/INT/F_INT_TO_DINT/)), `F_ADD` ([`F_ADD`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/arithmetic/F_ADD/)), `TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)), `RESULT` ([`PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/PUBLISH_1/): `QI` = `TRUE`, `ID` = `T14_RESULT_WRITE`) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten | Knoten | Bedeutung |
|---|---|---|
| `T14_NUM1_READ` | `/Objects/T14/Num1,1:s=T14_Num1` | Knoten auf B; B liest lokal (`READ`) |
| `T14_NUM2_READ` | `/Objects/T14/Num2,1:s=T14_Num2` | Knoten auf B; B liest lokal (`READ`) |
| `T14_RESULT_WRITE` | `/Objects/T14/Result,1:s=T14_Result` | Knoten auf B; B schreibt lokal (`WRITE`) |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A): leer, keine Verbindungen.

2. **`SubApp_PC_B`** (Gerät B), Ereignisverbindungen:

    1. `NUM1.IND` → `TO_DINT_1.REQ`
    2. `NUM2.IND` → `TO_DINT_2.REQ`
    3. `TO_DINT_1.CNF` → `F_ADD.REQ`
    4. `TO_DINT_2.CNF` → `F_ADD.REQ`
    5. `F_ADD.CNF` → `TO_INT.REQ`
    6. `TO_INT.CNF` → `RESULT.REQ`
    7. `NUM1.INITO` → `NUM2.INIT`
    8. `NUM2.INITO` → `RESULT.INIT`

3. **`SubApp_PC_B`** (Gerät B), Datenverbindungen:

    1. `NUM1.RD_1` → `TO_DINT_1.IN`
    2. `NUM2.RD_1` → `TO_DINT_2.IN`
    3. `TO_DINT_1.OUT` → `F_ADD.IN1`
    4. `TO_DINT_2.OUT` → `F_ADD.IN2`
    5. `F_ADD.OUT` → `TO_INT.IN`
    6. `TO_INT.OUT` → `RESULT.SD_1`

## Technische Besonderheiten

- **Nur lokale Zugriffe**: alle drei Knoten werden auf B mit `F_OPC_LOCAL` (`READ` bzw. `WRITE`) angesprochen; Gerät A enthält in diesem Training keine Logik.
- **Typumwandlung**: die abonnierten Werte werden mit `F_INT_TO_DINT` auf `DINT` gewandelt, mit `F_ADD` addiert und das Ergebnis mit `F_DINT_TO_INT` wieder auf `INT` gewandelt, bevor es veröffentlicht wird.

## Hinweis

- **Nicht auf dem Gerät getestet.** `Training_13_OPC_UA_RES` bis `Training_18_OPC_UA_RES` wurden noch nicht auf einem Gerät ausgeführt. Diese Seite beschreibt nur, was in `test_Training_14_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` und `myOpcUaAddresses.gcf` steht; ob die Beispiele wie beschrieben laufen, ist nicht geprüft.
- Die Initialisierung kommt aus den Resources: In `EMB_RES_A` und `EMB_RES_B` führt `START.COLD` auf `E_TRIG` (`EVENTTYPE = 'EInit'`), das die Initialisierung (`EInit`) der Application auslöst. Deshalb hat der jeweils erste Baustein einer Kette in der SubApp keine eigene Verbindung auf seinen `INIT`-Eingang.

## Lernziele

- Das Adder-Beispiel aus `opcua.adoc` mit `SUBSCRIBE` und `PUBLISH` aufbauen.
- Werte vom Typ `INT` für die Addition umwandeln.

## Zusammenfassung

`Training_14_OPC_UA_RES` ist der Adder aus `opcua.adoc`: Gerät B bildet `Result` = `Num1` + `Num2` über `SUBSCRIBE`, `F_ADD` und `PUBLISH`; die SubApp von Gerät A ist leer.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
