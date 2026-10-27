# Uebung_OPC_L09_AX: Toggle-Flipflop lokal per Methodenaufruf (Adapter-Variante)

![Uebung_OPC_L09_AX_network](./Uebung_OPC_L09_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L09_AX` ist die lokale Variante von `Training_09_OPC_UA_RES` (Adapter-Variante von Training_08): Funktional identisch zu L08, aber mit den einwertigen `AE_AX_*`-Adapterbausteinen (`AE` = Adapter-Event, ein einzelner Trigger ohne Set/Reset-Unterscheidung). Beide Hälften der Vorlage laufen auf **einem** Gerät; die OPC-UA-Adressen sind dieselben wie in L08 (`FF1_*`).

Das Netzwerk besteht aus zwei SubApps ohne Verbindungen untereinander; die Kopplung läuft ausschließlich über die OPC-UA-Adressen.

Übersicht aller lokalen Übungen: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

## Verwendete Funktionsbausteine (FBs)

| Name | Typ | Parameter | Erklärung |
|---|---|---|---|
| `SoftKeyT_PC_A_OPC_Adapter` | [`SoftKeyT_PC_A_OPC_Adapter`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeyT_PC_A_OPC_Adapter/) | `u16ObjId` = `SoftKey_F1`, `ID_TRIGGER_CALL` = `FF1_TRIGGER_CALL`, `ID_STATE_READ` = `FF1_STATE_READ` | Clientseite: `SoftKeyT_AE_AX` bündelt SoftKey und `GreenWhiteBackground` hinter einem `AE_AX`-Plug; `AE_AX_CLIENT_0_SUBSCRIBE_1` (`TRIGGER`) ruft die Methode per `CALL_METHOD` auf und liest den Zustand per `SUBSCRIBE`. |
| `SoftKeyT_PC_B_OPC_Adapter` | [`SoftKeyT_PC_B_OPC_Adapter_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/SoftKeyT_PC_B_OPC_Adapter_LOC/) | `Output` = `Output_Q1`, `ID_TRIGGER_METHOD` = `FF1_TRIGGER_METHOD`, `ID_STATE_WRITE` = `FF1_STATE_WRITE` | Serverseite (lokale Variante `_LOC`): `AE_AX_SERVER_0_PUBLISH_1` legt die Methode lokal an und schreibt den Zustand lokal per `PUBLISH`; `AE_AX_AX_SPLIT` verteilt an `AE_AX_T_FF` (`FLIPFLOP`) und an `DigitalOutput_Q1`. |

## OPC-UA-Adressraum

Die IDs stammen aus `const\OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`) und werden mit den Functions der Lib `MyLib_OPC` berechnet (`F_OPC_LOCAL`, `F_OPC_REMOTE`).

| Konstante | Aktion | Knoten / Methode | Bedeutung |
|---|---|---|---|
| `FF1_TRIGGER_METHOD` | `CREATE_METHOD` | `/Objects/ToggleFF/FF1_Trigger` | Toggle-Methode lokal anlegen |
| `FF1_TRIGGER_CALL` | `CALL_METHOD` | `/Objects/ToggleFF/FF1_Trigger` @ `opc.tcp://localhost:4840` | Methode per localhost aufrufen |
| `FF1_STATE_WRITE` | `WRITE` | `/Objects/ToggleFF/FF1_State,1:s=FF1_State` | Zustand lokal schreiben (PUBLISH) |
| `FF1_STATE_READ` | `READ` | `/Objects/ToggleFF/FF1_State,1:s=FF1_State` | Zustand lokal lesen (SUBSCRIBE) |

## Programmablauf und Verbindungen

1. **`SoftKeyT_PC_A_OPC_Adapter`**: keine Verbindungen im Netzwerk der Übung (die Verdrahtung steckt im Composite).
2. **`SoftKeyT_PC_B_OPC_Adapter`**: keine Verbindungen im Netzwerk der Übung (die Verdrahtung steckt im Composite).

## Technische Besonderheiten

- **`AE_AX_*` als einwertige Basis-Kombi**: ein einzelner `TRIGGER`-Pin statt `S_R` (L07), da ein reiner Toggle-Trigger keine Aktionsunterscheidung braucht.
- **Kein Netzwerk zwischen Client und Server**: Die Kopplung läuft nur über die OPC-UA-Knoten; `FF1_TRIGGER_CALL` zielt per `LOCAL_OPC` (`opc.tcp://localhost:4840`) auf die eigene Station.

## Lernziele

- Toggle-Flipflop mit Methodenaufruf auf einem Gerät aufbauen.
- Lokale Bausteinvarianten (`_LOC`) und `CALL_METHOD` per `localhost` kombinieren.
- Zustandsrückmeldung lokal per `PUBLISH`/`SUBSCRIBE` auf den SoftKey-Hintergrund führen.

**Schwierigkeitsgrad**: Fortgeschritten  
**Empfohlene Reihenfolge**: `Uebung_OPC_L08_AX`, `Training_09_OPC_UA_RES` (Vorlage, zwei Geräte)

## Hinweis / Ergebnis

- **Nicht auf dem Gerät getestet.** Auf `FORTE_PC_AX` getestet sind bisher nur L01, L03 und L19; `Uebung_OPC_L09_AX` wurde noch nicht auf dem Gerät ausgeführt.
- Lokal (ohne Endpunkt) laufen `READ`/`SUBSCRIBE`, `WRITE`/`PUBLISH`, `CREATE_METHOD`/`SERVER` und `PUBLISH_0`. `CALL_METHOD` und die Client-Subscription laufen dagegen mit Endpunkt an die eigene Station (`localhost` oder eigene IP; beides zählt als remote).
Hinweis: Jeder `SERVER_0` braucht `IND` → `RSP` direkt am eigenen Baustein, sonst stocken bis zu 4 s lang alle OPC-UA-Publishes der Station (Regel siehe [Übersicht](OPC_UA_Lokal.md)). Die SubApp `MyLib::net::AE_AX_SERVER_0_PUBLISH_1` enthält die Selbstschleife.

## Zusammenfassung

`Uebung_OPC_L09_AX` ist die `AE_AX_*`-Adaptervariante von L08: dieselbe Funktion und dieselben OPC-UA-Adressen, auf einem Gerät mit Methodenaufruf per `localhost`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
