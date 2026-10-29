# Uebung_OPC_L18_AX: Objekte und Variablen anlegen und löschen mit PUBLISH_0

![Uebung_OPC_L18_AX_network](./Uebung_OPC_L18_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L18_AX` legt mit vier `PUBLISH_0` lokal (ohne Endpunkt) im eigenen OPC-UA-Server ein Objekt und eine Variable an und löscht sie wieder: `Input_I1` legt das Objekt an, `Input_I2` die Variable, `Input_I3` löscht die Variable, `Input_I4` löscht das Objekt. Die ID der `PUBLISH_0`-Bausteine ist jeweils direkt eine `opc_ua[...]`-Zeichenkette.

Übersicht der Serie: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

Kommentar im `.SUB`: „Objekte/Variablen per PUBLISH_0: I1 CREATE_OBJECT, I2 CREATE_VARIABLE, I3 DELETE_VARIABLE, I4 DELETE_OBJECT - lokal auf einem Geraet“

## Verwendete Funktionsbausteine (FBs)

### `TASTER_I1`

- Typ: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
- Parameter:

    - `QI` = `TRUE`
    - `Input` = `Input_I1`
    - `InputEvent` = `BUTTON_PRESS_DOWN`

- Erklärung: Taster `Input_I1` (Ereignis `BUTTON_PRESS_DOWN`, also beim Drücken). Löst `OBJ_CREATE` aus.

### `OBJ_CREATE`

- Typ: `iec61499::net::PUBLISH_0`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L18_OBJ_CREATE`

- Erklärung: `PUBLISH_0` ohne Daten, lokal (ohne Endpunkt): legt das Objekt `L18_MyObject` unter `/Objects` an (`CREATE_OBJECT`, Typ `BaseObjectType` = `i=58`). Die ID ist hier direkt eine `opc_ua[...]`-Zeichenkette.

### `TASTER_I2`

- Typ: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
- Parameter:

    - `QI` = `TRUE`
    - `Input` = `Input_I2`
    - `InputEvent` = `BUTTON_PRESS_DOWN`

- Erklärung: Taster `Input_I2` (Ereignis `BUTTON_PRESS_DOWN`, also beim Drücken). Löst `VAR_CREATE` aus.

### `VAR_CREATE`

- Typ: `iec61499::net::PUBLISH_0`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L18_VAR_CREATE`

- Erklärung: `PUBLISH_0` ohne Daten, lokal (ohne Endpunkt): legt die Variable `L18_MyVariable` unter `/Objects` an (`CREATE_VARIABLE`, `BaseDataVariableType` = `i=63`, Datentyp Int32 = `i=6`). Die ID ist hier direkt eine `opc_ua[...]`-Zeichenkette.

### `TASTER_I3`

- Typ: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
- Parameter:

    - `QI` = `TRUE`
    - `Input` = `Input_I3`
    - `InputEvent` = `BUTTON_PRESS_DOWN`

- Erklärung: Taster `Input_I3` (Ereignis `BUTTON_PRESS_DOWN`, also beim Drücken). Löst `VAR_DELETE` aus.

### `VAR_DELETE`

- Typ: `iec61499::net::PUBLISH_0`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L18_VAR_DELETE`

- Erklärung: `PUBLISH_0` ohne Daten, lokal (ohne Endpunkt): löscht die Variable `L18_MyVariable` (`DELETE_VARIABLE`). Die ID ist hier direkt eine `opc_ua[...]`-Zeichenkette.

### `TASTER_I4`

- Typ: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
- Parameter:

    - `QI` = `TRUE`
    - `Input` = `Input_I4`
    - `InputEvent` = `BUTTON_PRESS_DOWN`

- Erklärung: Taster `Input_I4` (Ereignis `BUTTON_PRESS_DOWN`, also beim Drücken). Löst `OBJ_DELETE` aus.

### `OBJ_DELETE`

- Typ: `iec61499::net::PUBLISH_0`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L18_OBJ_DELETE`

- Erklärung: `PUBLISH_0` ohne Daten, lokal (ohne Endpunkt): löscht das Objekt `L18_MyObject` (`DELETE_OBJECT`). Die ID ist hier direkt eine `opc_ua[...]`-Zeichenkette.

## OPC-UA-Adressen (`OpcUaLokal.gcf`)

Die IDs stammen aus `const/OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`).

| Konstante | Aktion | Knoten / ID |
|---|---|---|
| `L18_OBJ_CREATE` | CREATE_OBJECT | `opc_ua[CREATE_OBJECT;,i=58;/Objects/1:L18_MyObject]` |
| `L18_VAR_CREATE` | CREATE_VARIABLE | `opc_ua[CREATE_VARIABLE;,i=63;,i=6;/Objects/1:L18_MyVariable]` |
| `L18_VAR_DELETE` | DELETE_VARIABLE | `opc_ua[DELETE_VARIABLE;/Objects/1:L18_MyVariable]` |
| `L18_OBJ_DELETE` | DELETE_OBJECT | `opc_ua[DELETE_OBJECT;/Objects/1:L18_MyObject]` |

## Programmablauf und Verbindungen

### Ereignisverbindungen

1. `TASTER_I1.IND` → `OBJ_CREATE.REQ`
2. `TASTER_I1.INITO` → `OBJ_CREATE.INIT`
3. `TASTER_I2.IND` → `VAR_CREATE.REQ`
4. `TASTER_I2.INITO` → `VAR_CREATE.INIT`
5. `TASTER_I3.IND` → `VAR_DELETE.REQ`
6. `TASTER_I3.INITO` → `VAR_DELETE.INIT`
7. `TASTER_I4.IND` → `OBJ_DELETE.REQ`
8. `TASTER_I4.INITO` → `OBJ_DELETE.INIT`

## Hinweis / Ergebnis

Nicht auf dem Gerät getestet. Erwartet: Nach `Input_I1`/`Input_I2` sind Objekt und Variable z. B. in UaExpert sichtbar, nach `Input_I3`/`Input_I4` wieder verschwunden.

Dasselbe Beispiel auf zwei Geräten: [Training_18_OPC_UA_RES](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_VV/03_OPC_UA/Training_18_OPC_UA_RES/Training_18_OPC_UA_RES/).

## Lernziele

- Mit `PUBLISH_0` Objekte und Variablen im Adressraum anlegen (`CREATE_OBJECT`, `CREATE_VARIABLE`) und löschen (`DELETE_VARIABLE`, `DELETE_OBJECT`)
- Aufbau einer `opc_ua[...]`-ID aus Aktion, Typ-Knoten (`i=58`, `i=63`, `i=6`) und Pfad
- Ein Taster je Aktion: `IND` löst `REQ` aus, `INITO` startet `INIT`

## Zusammenfassung

Die Übung zeigt die Verwaltung des eigenen Adressraums mit `PUBLISH_0`: je ein Taster pro Anlegen/Löschen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
