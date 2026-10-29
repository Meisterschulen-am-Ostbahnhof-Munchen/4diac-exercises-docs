# Uebung_OPC_L05_AX: SoftKeys Set/Reset per OPC-UA-Methode lokal

![Uebung_OPC_L05_AX_network](./Uebung_OPC_L05_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L05_AX` entspricht [`Training_05_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_05_OPC_UA_RES/Training_05_OPC_UA_RES.md) (test_VV), aber lokal: Die SoftKeys `SoftKey_F1` und `SoftKey_F2` rufen je eine eigene OPC-UA-Methode (Set, Reset) per localhost auf. Ein SR-Flipflop schaltet `Output_Q1` und meldet seinen Zustand lokal per `PUBLISH` zurück; `GreenWhiteBackground` am Set-SoftKey zeigt ihn an. Die Schnittstelle der SubApp ist leer.

`LOCAL_OPC` ist `"opc.tcp://localhost:4840"` (eigener Server) und wird nur für `CALL_METHOD` benötigt: Ein `CALL_METHOD` ist lokal nicht erlaubt und läuft deshalb als Remote-Aufruf mit Endpunkt an die eigene Station (localhost oder eigene IP; beides zählt als remote).

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `SoftKeySR_PC_A_OPC` | [`SoftKeySR_PC_A_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeySR_PC_A_OPC/) | `u16ObjId_SET` = `SoftKey_F1`, `u16ObjId_RESET` = `SoftKey_F2`, `ID_SET_CALL` = `SR2_SET_CALL`, `ID_RESET_CALL` = `SR2_RESET_CALL`, `ID_STATE_READ` = `SR2_STATE_READ` | Zwei [`Softkey_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IE/) (`SK_RELEASED`) lösen je einen [`CLIENT_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/CLIENT_0/) aus; [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) liest den Zustand und speist [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) am Set-SoftKey |
| `SoftKeySR_PC_B_OPC` | [`SoftKeySR_PC_B_OPC_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeySR_PC_B_OPC_LOC/) | `Output` = `Output_Q1`, `ID_SET_METHOD` = `SR2_SET_METHOD`, `ID_RESET_METHOD` = `SR2_RESET_METHOD`, `ID_STATE_WRITE` = `SR2_STATE_WRITE` | Zwei [`SERVER_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SERVER_0/) (Set/Reset) speisen [`AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/); [`AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/) verteilt den Zustand an [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) (`Output_Q1`) und [`AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) (lokales `PUBLISH`). Typ ist `SoftKeySR_PC_B_OPC_LOC` |

## OPC-UA-Adressraum

Alle Konstanten stehen in `const/OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`). Die IDs werden mit [`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/) (ohne Endpunkt) und [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/) (mit Endpunkt) aus je einem `*_NODE` berechnet.

`LOCAL_OPC` ist `"opc.tcp://localhost:4840"` (eigener Server) und wird nur für `CALL_METHOD` benötigt: Ein `CALL_METHOD` ist lokal nicht erlaubt und läuft deshalb als Remote-Aufruf mit Endpunkt an die eigene Station (localhost oder eigene IP; beides zählt als remote).

| Konstante | Wert | Verwendet von |
|---|---|---|
| `SR2_SET_NODE` / `SR2_RESET_NODE` | `"/Objects/SRFlipflop/SR2_Set"` / `…/SR2_Reset` | Methodenknoten |
| `SR2_SET_METHOD` / `SR2_RESET_METHOD` | `MyLib_OPC::F_OPC_LOCAL("CREATE_METHOD", SR2_…_NODE)` | `SoftKeySR_PC_B_OPC` (`SERVER_0`, lokal angelegt) |
| `SR2_SET_CALL` / `SR2_RESET_CALL` | `MyLib_OPC::F_OPC_REMOTE("CALL_METHOD", LOCAL_OPC, SR2_…_NODE)` | `SoftKeySR_PC_A_OPC` (`CLIENT_0`, Aufruf per localhost) |
| `SR2_STATE_NODE` | `"/Objects/SRFlipflop/SR2_State,1:s=SR2_State"` | Knoten Zustand |
| `SR2_STATE_WRITE` | `MyLib_OPC::F_OPC_LOCAL("WRITE", SR2_STATE_NODE)` | `SoftKeySR_PC_B_OPC` (`ID_STATE_WRITE`) |
| `SR2_STATE_READ` | `MyLib_OPC::F_OPC_LOCAL("READ", SR2_STATE_NODE)` | `SoftKeySR_PC_A_OPC` (`ID_STATE_READ`) |

## Programmablauf und Verbindungen

1. **Zwei SubApps ohne Modellverbindung**: Die `.SUB` enthält `SoftKeySR_PC_A_OPC` (`x=0`) und `SoftKeySR_PC_B_OPC` (`x=6000`) und keine Verbindungen; die Kopplung läuft über OPC-UA.
2. **`SoftKeySR_PC_A_OPC`**: `SoftKey_SET.IND` und `SoftKey_RESET.IND` lösen `TRIGGER_SET_CLIENT.REQ` bzw. `TRIGGER_RESET_CLIENT.REQ` aus; die `CLIENT_0` rufen `SR2_SET_CALL` und `SR2_RESET_CALL` an localhost auf. `STATE_SUBSCRIBE.OUT` geht per Adapterverbindung an `GreenWhiteBackground_AX.DI1`.
3. **`SoftKeySR_PC_B_OPC`**: `TRIGGER_SET_SERVER.IND` → `AX_SR.S`, `TRIGGER_RESET_SERVER.IND` → `AX_SR.R`; jede `IND` wird zusätzlich mit `RSP` des eigenen Servers beantwortet. `AX_SR.Q` → `AX_SPLIT_2.IN`; `OUT1` → `DigitalOutput_Q1.OUT`, `OUT2` → `STATE_PUBLISH.IN`.
4. **Laufzeit**: F1 setzt, F2 rücksetzt. Der Zustand wirkt auf `Output_Q1` und kommt per lokalem `PUBLISH`/`SUBSCRIBE` zurück.

## Ergebnis

Nicht auf dem Gerät getestet.
Hinweis: Jeder `SERVER_0` braucht `IND` → `RSP` direkt am eigenen Baustein, sonst stocken bis zu 4 s lang alle OPC-UA-Publishes der Station (Regel siehe [Übersicht](OPC_UA_Lokal.md)).

## Lernziele

- Zwei getrennte OPC-UA-Methoden (Set/Reset) mit einem SR-Flipflop als Gegenstück.
- `CALL_METHOD` per localhost und `CREATE_METHOD` lokal auf demselben Gerät.
- Zustandsrückweg lokal über `PUBLISH`/`SUBSCRIBE`.

**Empfohlene Reihenfolge**: `Uebung_OPC_L04_AX`, [`Training_05_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_05_OPC_UA_RES/Training_05_OPC_UA_RES.md)

## Zusammenfassung

`Uebung_OPC_L05_AX` ist die Set/Reset-Variante von L04: zwei SoftKeys, zwei Methoden per localhost, ein SR-Flipflop an `Output_Q1`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
