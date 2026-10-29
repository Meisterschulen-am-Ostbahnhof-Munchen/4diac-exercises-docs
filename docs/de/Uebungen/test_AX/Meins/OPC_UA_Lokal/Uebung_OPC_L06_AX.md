# Uebung_OPC_L06_AX: Adapter-Variante von L04: Set/Reset/Toggle per OPC-UA-Methode lokal

![Uebung_OPC_L06_AX_network](./Uebung_OPC_L06_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L06_AX` entspricht [`Training_06_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_06_OPC_UA_RES/Training_06_OPC_UA_RES.md) (test_VV, Adapter-Variante von Training_04), aber lokal. Funktional ist sie gleich zu L04 (Set/Reset/Toggle per localhost, Zustand lokal per `PUBLISH`), mit denselben IDs `SR1_*`. Der Unterschied liegt in der Bündelung: HMI und OPC-UA-Trigger sind getrennt und über einen `ASRT_AX`-Adapter verbunden. Die Schnittstelle der SubApp ist leer.

`LOCAL_OPC` ist `"opc.tcp://localhost:4840"` (eigener Server) und wird nur für `CALL_METHOD` benötigt: Ein `CALL_METHOD` ist lokal nicht erlaubt und läuft deshalb als Remote-Aufruf mit Endpunkt an die eigene Station (localhost oder eigene IP; beides zählt als remote).

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter` | [`Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter/) | `u16ObjId_SET` = `SoftKey_F1`, `u16ObjId_RESET` = `SoftKey_F2`, `u16ObjId_TOGGLE` = `SoftKey_F3`, `ID_SET_CALL` = `SR1_SET_CALL`, `ID_RESET_CALL` = `SR1_RESET_CALL`, `ID_TOGGLE_CALL` = `SR1_TOGGLE_CALL`, `ID_STATE_READ` = `SR1_STATE_READ` | [`SoftKeySRT_ASRT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeySRT_ASRT_AX/) bündelt die HMI (3 SoftKeys + `GreenWhiteBackground`) hinter einem `ASRT_AX`-Plug; [`ASRT_AX_CLIENT_0_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/ASRT_AX_CLIENT_0_SUBSCRIBE_1/) bündelt die drei `CLIENT_0` + `AX_SUBSCRIBE_1` hinter einem `ASRT_AX`-Socket |
| `SRT_RPC_FROM_Remote_QXA_OPC_Adapter` | [`SRT_RPC_FROM_Remote_QXA_OPC_Adapter_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SRT_RPC_FROM_Remote_QXA_OPC_Adapter_LOC/) | `Output` = `Output_Q1`, `ID_SET_METHOD` = `SR1_SET_METHOD`, `ID_RESET_METHOD` = `SR1_RESET_METHOD`, `ID_TOGGLE_METHOD` = `SR1_TOGGLE_METHOD`, `ID_STATE_WRITE` = `SR1_STATE_WRITE` | [`ASRT_AX_SERVER_0_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/ASRT_AX_SERVER_0_PUBLISH_1/) bündelt die drei `SERVER_0` + `AX_PUBLISH_1`; [`ASRT_AX_AX_SPLIT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/bidirectional/ASRT_AX_AX_SPLIT/) verteilt an [`ASRT_AX_T_FF_SR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/bidirectional/ASRT_AX_T_FF_SR_2/) und [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) (`Output_Q1`). Typ ist `…_Adapter_LOC` |

## OPC-UA-Adressraum

Die Konstanten sind dieselben wie in L04 (`SR1_*`).


`LOCAL_OPC` ist `"opc.tcp://localhost:4840"` (eigener Server) und wird nur für `CALL_METHOD` benötigt: Ein `CALL_METHOD` ist lokal nicht erlaubt und läuft deshalb als Remote-Aufruf mit Endpunkt an die eigene Station (localhost oder eigene IP; beides zählt als remote).

| Konstante | Wert | Verwendet von |
|---|---|---|
| `SR1_SET_NODE` / `SR1_RESET_NODE` / `SR1_TOGGLE_NODE` | `"/Objects/SRFlipflop/SR1_Set"` / `…/SR1_Reset` / `…/SR1_Toggle` | Methodenknoten |
| `SR1_SET_METHOD` / `SR1_RESET_METHOD` / `SR1_TOGGLE_METHOD` | `MyLib_OPC::F_OPC_LOCAL("CREATE_METHOD", SR1_…_NODE)` | `SRT_RPC_FROM_Remote_QXA_OPC_Adapter` (`ID_*_METHOD`, `SERVER_0`, lokal angelegt) |
| `SR1_SET_CALL` / `SR1_RESET_CALL` / `SR1_TOGGLE_CALL` | `MyLib_OPC::F_OPC_REMOTE("CALL_METHOD", LOCAL_OPC, SR1_…_NODE)` | `Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter` (`ID_*_CALL`, `CLIENT_0`, Aufruf per localhost) |
| `SR1_STATE_NODE` | `"/Objects/SRFlipflop/SR1_State,1:s=SR1_State"` | Knoten Zustand |
| `SR1_STATE_WRITE` | `MyLib_OPC::F_OPC_LOCAL("WRITE", SR1_STATE_NODE)` | `SRT_RPC_FROM_Remote_QXA_OPC_Adapter` (`ID_STATE_WRITE`) |
| `SR1_STATE_READ` | `MyLib_OPC::F_OPC_LOCAL("READ", SR1_STATE_NODE)` | `Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter` (`ID_STATE_READ`) |

## Programmablauf und Verbindungen

1. **Zwei SubApps ohne Modellverbindung**: Die `.SUB` enthält `Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter` (`x=0`) und `SRT_RPC_FROM_Remote_QXA_OPC_Adapter` (`x=6000`) und keine Verbindungen; die Kopplung läuft über OPC-UA.
2. **`Softkey_SRT_RPC_TO_Remote_BG_OPC_Adapter`**: `INIT` startet zuerst `TRIGGER` (`ASRT_AX_CLIENT_0_SUBSCRIBE_1`), dessen `INITO` startet `SoftKeySRT_ASRT_AX`. Der `ASRT_AX`-Plug `SoftKeySRT_ASRT_AX.OUT` ist mit dem Socket `TRIGGER.S_R_T` verbunden; die IDs `ID_SET_CALL`, `ID_RESET_CALL`, `ID_TOGGLE_CALL` und `ID_STATE_READ` gehen an `TRIGGER.ID_SET`, `.ID_RESET`, `.ID_TOGGLE` bzw. `.ID_STATE`.
3. **`SRT_RPC_FROM_Remote_QXA_OPC_Adapter`**: `INIT` startet `TRIGGER` (`ASRT_AX_SERVER_0_PUBLISH_1`), dessen `INITO` startet `DigitalOutput_Q1`. `TRIGGER.S_R_T` → `SPLIT.IN` (`ASRT_AX_AX_SPLIT`); `SPLIT.OUT` → `FLIPFLOP.S_R_T` (`ASRT_AX_T_FF_SR_2`); `SPLIT.AX_OUT` → `DigitalOutput_Q1.OUT`. Die IDs `ID_SET_METHOD`, `ID_RESET_METHOD`, `ID_TOGGLE_METHOD` und `ID_STATE_WRITE` gehen an `TRIGGER.ID_SET`, `.ID_RESET`, `.ID_TOGGLE` bzw. `.ID_STATE`.
4. **Laufzeit**: wie L04. F1 setzt, F2 rücksetzt, F3 schaltet um; der Zustand wirkt auf `Output_Q1` und wird lokal per `PUBLISH` gemeldet.

## Ergebnis

Nicht auf dem Gerät getestet.
Hinweis: Jeder `SERVER_0` braucht `IND` → `RSP` direkt am eigenen Baustein, sonst stocken bis zu 4 s lang alle OPC-UA-Publishes der Station (Regel siehe [Übersicht](OPC_UA_Lokal.md)). Die SubApp `MyLib::net::ASRT_AX_SERVER_0_PUBLISH_1` enthält die Selbstschleife.

## Lernziele

- Bündelung über `ASRT_AX`-Adapter: HMI und OPC-UA-Trigger getrennt, ein Adapter statt vieler Einzelverbindungen.
- Gleiche Funktion wie L04 mit anderer Struktur (manuell verdrahtet gegen Adapter).
- `CALL_METHOD` per localhost und `CREATE_METHOD` lokal auf demselben Gerät.

**Empfohlene Reihenfolge**: `Uebung_OPC_L04_AX`, [`Training_06_OPC_UA_RES`](../../../test_VV/03_OPC_UA/Training_06_OPC_UA_RES/Training_06_OPC_UA_RES.md)

## Zusammenfassung

`Uebung_OPC_L06_AX` ist funktional gleich zu L04, nutzt aber die `ASRT_AX`-Adapter-Composites: HMI und OPC-UA-Trigger sind getrennt und über einen Adapter verbunden, die Zustandsmeldung läuft lokal per `PUBLISH`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
