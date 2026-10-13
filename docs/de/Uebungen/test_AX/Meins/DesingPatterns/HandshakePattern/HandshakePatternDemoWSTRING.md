# HandshakePatternDemoWSTRING: Handshake-Muster mit WSTRING-Nutzdaten

![HandshakePatternDemoWSTRING_network](./HandshakePatternDemoWSTRING_network.svg)

* * * * * * * * * *

## Einleitung

`HandshakePatternDemoWSTRING` ist die Variante von [`HandshakePatternDemo`](./HandshakePatternDemo.md) mit Nutzdaten: Requester (Plug) und Responder (Socket) sind über **eine einzige** `EVENT_HS_WSTRING`-Adapterverbindung gekoppelt, die `REQ`/`CNF`/`IND`/`RSP` jeweils samt `WSTRING`-Nutzdaten transportiert (Kommentar im Typ).

Zusätzlich zur Schnittstelle des Basis-Demos (`INIT`, `START`, `TRIGGER`, `INITO`, `DONE`, `NOTIFIED`, `QI`, `QO`, fünf `UINT`-Zähler) gibt es die Eingänge `ReqPayload` (Vorgabe `"push,100"`) und `IndPayload` (Vorgabe `"status,ok"`) sowie die Ausgänge `Requester_CnfPayload`, `Requester_IndPayload`, `Responder_LastReqPayload` und `Responder_LastRspPayload` (alle `WSTRING`).

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Requester` | `Meins::DesingPatterns::HandshakePattern::HandshakeRequesterWSTRING` | - | Plug `HS` (`EVENT_HS_WSTRING`): sendet `REQ` mit `ReqPayload`, empfängt `CNF`/`IND` mit Nutzdaten und antwortet auf `IND` mit `RSP`. |
| `Responder` | `Meins::DesingPatterns::HandshakePattern::HandshakeResponderWSTRING` | - | Socket `HS`: empfängt `REQ`/`RSP`, sendet `CNF` und (bei `TRIGGER`) `IND` mit `IndPayload`. |

## Programmablauf und Verbindungen

**Adapterverbindung:** `Requester.HS` → `Responder.HS` (Requester = Plug ist Quelle, Responder = Socket ist Ziel).

**Ereignisverbindungen:** `INIT` → `Requester.INIT` → `Requester.INITO` → `Responder.INIT` → `Responder.INITO` → `INITO`; `START` → `Requester.START`; `TRIGGER` → `Responder.TRIGGER`; `Requester.DONE` → `DONE`; `Requester.NOTIFIED` → `NOTIFIED`.

**Datenverbindungen:**

1. `QI` → `Requester.QI`; `Requester.QO` → `Responder.QI`; `Responder.QO` → `QO`.
2. `ReqPayload` → `Requester.ReqPayload`; `IndPayload` → `Responder.IndPayload`.
3. `Requester.CnfPayload`, `Requester.IndPayload`, `Requester.CnfCount`, `Requester.IndCount` → gleichnamige Ausgänge `Requester_*`.
4. `Responder.LastReqPayload`, `Responder.LastRspPayload`, `Responder.ReqCount`, `Responder.IndCount`, `Responder.RspCount` → Ausgänge `Responder_*`.

## Zusammenfassung

Wie das Basis-Demo, jedoch transportiert dieselbe eine Adapterverbindung zusätzlich Text-Nutzdaten (`WSTRING`) in beide Richtungen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
