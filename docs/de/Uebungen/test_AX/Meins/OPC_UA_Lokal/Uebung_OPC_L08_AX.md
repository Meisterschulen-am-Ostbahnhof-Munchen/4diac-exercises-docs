# Uebung_OPC_L08_AX: Toggle-Flipflop lokal per Methodenaufruf

![Uebung_OPC_L08_AX_network](./Uebung_OPC_L08_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L08_AX` ist die lokale Variante von `Training_08_OPC_UA_RES`: `SoftKey_F1` ruft per `localhost` die Toggle-Methode auf, ein Toggle-Flipflop schaltet `Output_Q1`, und der Zustand kommt lokal per `PUBLISH`/`SUBSCRIBE` zurück auf den SoftKey-Hintergrund. Beide Hälften der Vorlage laufen auf **einem** Gerät.

Das Netzwerk besteht aus zwei SubApps ohne Verbindungen untereinander; die Kopplung läuft ausschließlich über die OPC-UA-Adressen.

Übersicht aller lokalen Übungen: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Verwendete Funktionsbausteine (FBs)

| Name | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Softkey_Toggle_RPC_TO_Remote_BG_OPC` | [`Softkey_Toggle_RPC_TO_Remote_BG_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey_Toggle_RPC_TO_Remote_BG_OPC/) | `u16ObjId` = `SoftKey_F1`, `ID_TRIGGER_CALL` = `FF1_TRIGGER_CALL`, `ID_STATE_READ` = `FF1_STATE_READ` | Clientseite: `Softkey_IE` (Ereignis `SK_RELEASED`) löst `CLIENT_0` aus (argumentloser Methodenaufruf); `AX_SUBSCRIBE_1` liest den Zustand und `GreenWhiteBackground1_AX` färbt den SoftKey (TRUE = grün, FALSE = weiß). |
| `Toggle_RPC_FROM_Remote_QXA_OPC_LOC` | [`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Toggle_RPC_FROM_Remote_QXA_OPC_LOC/) | `Output` = `Output_Q1`, `ID_TRIGGER_METHOD` = `FF1_TRIGGER_METHOD`, `ID_STATE_WRITE` = `FF1_STATE_WRITE` | Serverseite (lokale Variante `_LOC`): `SERVER_0` legt die Methode lokal an und bestätigt den Aufruf (`IND` → `RSP`); `AX_T_FF` toggelt, `AX_SPLIT_2` verteilt an `DigitalOutput_Q1` und an `AX_PUBLISH_1` (Zustand lokal schreiben). |

## OPC-UA-Adressraum

Die IDs stammen aus `const\OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`) und werden mit den Functions der Lib `MyLib_OPC` berechnet (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Konstante | Aktion | Knoten / Methode | Bedeutung |
|---|---|---|---|
| `FF1_TRIGGER_METHOD` | `CREATE_METHOD` | `/Objects/ToggleFF/FF1_Trigger` | Toggle-Methode lokal anlegen |
| `FF1_TRIGGER_CALL` | `CALL_METHOD` | `/Objects/ToggleFF/FF1_Trigger` @ `opc.tcp://localhost:4840` | Methode per localhost aufrufen |
| `FF1_STATE_WRITE` | `WRITE` | `/Objects/ToggleFF/FF1_State,1:s=FF1_State` | Zustand lokal schreiben (PUBLISH) |
| `FF1_STATE_READ` | `READ` | `/Objects/ToggleFF/FF1_State,1:s=FF1_State` | Zustand lokal lesen (SUBSCRIBE) |

## Programmablauf und Verbindungen

1. **`Softkey_Toggle_RPC_TO_Remote_BG_OPC`**: keine Verbindungen im Netzwerk der Übung (die Verdrahtung steckt im Composite).
2. **`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`**: keine Verbindungen im Netzwerk der Übung (die Verdrahtung steckt im Composite).

## Technische Besonderheiten

- **Manuelle Verdrahtung**: Im Gegensatz zu L09 sind SoftKey, Client, Flipflop und Split als einzelne Bausteine in den Composites sichtbar.
- **Kein Netzwerk zwischen Client und Server**: Die Kopplung läuft nur über die OPC-UA-Knoten; `FF1_TRIGGER_CALL` zielt per `LOCAL_OPC` (`opc.tcp://localhost:4840`) auf die eigene Station.

## Lernziele

- Toggle-Flipflop mit Methodenaufruf auf einem Gerät aufbauen.
- Lokale Bausteinvarianten (`_LOC`) und `CALL_METHOD` per `localhost` kombinieren.
- Zustandsrückmeldung lokal per `PUBLISH`/`SUBSCRIBE` auf den SoftKey-Hintergrund führen.

**Schwierigkeitsgrad**: Fortgeschritten  
**Empfohlene Reihenfolge**: `Training_08_OPC_UA_RES` (Vorlage, zwei Geräte), `Uebung_OPC_L03_AX`

## Hinweis / Ergebnis

- **Nicht auf dem Gerät getestet.** Auf `FORTE_PC_AX` getestet sind bisher nur L01, L03 und L19; `Uebung_OPC_L08_AX` wurde noch nicht auf dem Gerät ausgeführt.
- Lokal (ohne Endpunkt) laufen `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` und `PUBLISH_0`. `CALL_METHOD` und die Client-Subscription laufen dagegen mit Endpunkt an die eigene Station (`localhost` oder eigene IP; beides zählt als remote).
Hinweis: Jeder `SERVER_0` braucht `IND` → `RSP` direkt am eigenen Baustein, sonst stocken bis zu 4 s lang alle OPC-UA-Publishes der Station (Regel siehe [Übersicht](OPC_UA_Lokal.md)).

## Zusammenfassung

`Uebung_OPC_L08_AX` führt das Toggle-Muster von `Training_08_OPC_UA_RES` auf einem Gerät aus: Methodenaufruf per `localhost`, Zustand lokal per `PUBLISH`/`SUBSCRIBE`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
