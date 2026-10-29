# Training_18_OPC_UA_RES: Objekte und Variablen per PUBLISH_0 — zwei Geräte

![Training_18_OPC_UA_RES_network](./Training_18_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_18_OPC_UA_RES` setzt das Beispiel „Objekte/Variablen per `PUBLISH_0`“ aus `opcua.adoc` auf zwei Geräten um: Gerät B legt mit vier Tastern und je einem [`PUBLISH_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/PUBLISH_0/) ein OPC-UA-Objekt an (`Input_I1`), eine Variable direkt unter `/Objects` an, nicht im Objekt `T18_MyObject` (`Input_I2`), löscht die Variable (`Input_I3`) und löscht das Objekt (`Input_I4`). Gerät A liest die Variable beim Drücken von `Input_I1` mit [`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/) von Gerät B.

Das Training ist das Zwei-Geräte-Gegenstück zu `L18` in `test_AX/Meins/OPC_UA_Lokal`.

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_18_network.svg) | A (`192.168.1.11`) | `TASTER_I1` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_PRESS_DOWN`), `READ_VAR` ([`CLIENT_0_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_0_1/): `QI` = `TRUE`, `ID` = `T18_VAR_CLIENT_READ`), `VAR_TO_INT` ([`F_DINT_TO_INT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/iec61131-3/conversion/DINT/F_DINT_TO_INT/)) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_18_network.svg) | B (`192.168.1.12`) | Je Taster ein Paar aus `TASTER_Ix` ([`logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/): `QI` = `TRUE`, `Input` = `Input_Ix`, `InputEvent` = `BUTTON_PRESS_DOWN`) und [`PUBLISH_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/PUBLISH_0/) (`QI` = `TRUE`): `OBJ_CREATE` (`I1`, `ID` = `T18_OBJ_CREATE`), `VAR_CREATE` (`I2`, `ID` = `T18_VAR_CREATE`), `VAR_DELETE` (`I3`, `ID` = `T18_VAR_DELETE`), `OBJ_DELETE` (`I4`, `ID` = `T18_OBJ_DELETE`) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten | Knoten | Bedeutung |
|---|---|---|
| `T18_OBJ_CREATE` | `opc_ua[CREATE_OBJECT;,i=58;/Objects/1:T18_MyObject]` | B legt das Objekt `T18_MyObject` an (Typ `BaseObjectType`, `i=58`) |
| `T18_VAR_CREATE` | `opc_ua[CREATE_VARIABLE;,i=63;,i=6;/Objects/1:T18_MyVariable]` | B legt die Variable `T18_MyVariable` an (`BaseDataVariableType`, `i=63`; Datentyp `Int32`, `i=6`) |
| `T18_VAR_DELETE` | `opc_ua[DELETE_VARIABLE;/Objects/1:T18_MyVariable]` | B löscht die Variable |
| `T18_OBJ_DELETE` | `opc_ua[DELETE_OBJECT;/Objects/1:T18_MyObject]` | B löscht das Objekt |
| `T18_VAR_CLIENT_READ` | `/Objects/1:T18_MyVariable` | Knoten der angelegten Variable auf B; A liest sie per `CLIENT` (`READ` mit Endpunkt von B) |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A), Ereignisverbindungen:

    1. `TASTER_I1.IND` → `READ_VAR.REQ`
    2. `TASTER_I1.INITO` → `READ_VAR.INIT`
    3. `READ_VAR.CNF` → `VAR_TO_INT.REQ`

2. **`SubApp_PC_A`** (Gerät A), Datenverbindung:

    1. `READ_VAR.RD_1` → `VAR_TO_INT.IN`

3. **`SubApp_PC_B`** (Gerät B), Ereignisverbindungen (jeder Taster mit seinem eigenen `PUBLISH_0`):

    1. `TASTER_I1.IND` → `OBJ_CREATE.REQ`, `TASTER_I1.INITO` → `OBJ_CREATE.INIT`
    2. `TASTER_I2.IND` → `VAR_CREATE.REQ`, `TASTER_I2.INITO` → `VAR_CREATE.INIT`
    3. `TASTER_I3.IND` → `VAR_DELETE.REQ`, `TASTER_I3.INITO` → `VAR_DELETE.INIT`
    4. `TASTER_I4.IND` → `OBJ_DELETE.REQ`, `TASTER_I4.INITO` → `OBJ_DELETE.INIT`

## Technische Besonderheiten

- **Die ID ist der Befehl**: bei `PUBLISH_0` enthalten die Konstanten die komplette Aktion im Format `opc_ua[AKTION;...;Pfad]` (`CREATE_OBJECT`, `CREATE_VARIABLE`, `DELETE_VARIABLE`, `DELETE_OBJECT`) und werden direkt als `ID` verwendet; sie werden nicht mit `F_OPC_LOCAL`/`F_OPC_REMOTE` berechnet (siehe [OPC-UA-Adressen](../OPC_UA_Adressen.md)). Die Pfade enthalten den Namensraumindex `1:`.
- **Kein Datenanschluss auf B**: `PUBLISH_0` überträgt keine Daten; jeder Taster löst nur sein `REQ` aus.
- **Lesen auf A**: der Knoten der Variable (`T18_VAR_NODE`) wird mit `F_OPC_REMOTE` (`READ`, Endpunkt von B) angesprochen. Der gelesene Wert wird mit `F_DINT_TO_INT` gewandelt.
- **SubApp-Kommentar von A**: der Kommentar von `SubApp_PC_A` ist derselbe wie der von `SubApp_PC_B` (I1 … I4 für Anlegen/Löschen); in der SubApp von A ist nur das Lesen über `Input_I1` verdrahtet.

## Hinweis

- **Nicht auf dem Gerät getestet.** `Training_13_OPC_UA_RES` bis `Training_18_OPC_UA_RES` wurden noch nicht auf einem Gerät ausgeführt. Diese Seite beschreibt nur, was in `test_Training_18_OPC_UA.sys`, `SubApp_PC_A.SUB`, `SubApp_PC_B.SUB` und `myOpcUaAddresses.gcf` steht; ob die Beispiele wie beschrieben laufen, ist nicht geprüft.
- Die Initialisierung kommt aus den Resources: In `EMB_RES_A` und `EMB_RES_B` führt `START.COLD` auf `E_TRIG` (`EVENTTYPE = 'EInit'`), das die Initialisierung (`EInit`) der Application auslöst. Deshalb hat der jeweils erste Baustein einer Kette in der SubApp keine eigene Verbindung auf seinen `INIT`-Eingang.

## Lernziele

- Objekte und Variablen im OPC-UA-Adressraum eines Geräts mit `PUBLISH_0` anlegen und löschen.
- Eine so angelegte Variable von einem anderen Gerät mit `CLIENT_0_1` lesen.

## Zusammenfassung

`Training_18_OPC_UA_RES` ist das `PUBLISH_0`-Beispiel aus `opcua.adoc` auf zwei Geräten: B legt per `Input_I1`…`Input_I4` Objekt und Variable an bzw. löscht sie, A liest die Variable per `Input_I1`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
