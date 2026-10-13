# HandshakePatternDemo: Handshake-Muster (Requester/Responder)

![HandshakePatternDemo_network](./HandshakePatternDemo_network.svg)

* * * * * * * * * *

## Einleitung

`HandshakePatternDemo` zeigt das Handshake-Muster: ein Requester (Plug) ist mit einem Responder (Socket) über **eine einzige** `EVENT_HS`-Adapterverbindung gekoppelt (Kommentar im Typ). Diese eine Verbindung ersetzt vier getrennte Ereignisverbindungen `REQ`/`CNF`/`IND`/`RSP`.

Schnittstelle der SubApp: Ereigniseingänge `INIT`, `START` (Requester sendet ein `REQ`) und `TRIGGER` (Responder sendet ein unaufgefordertes `IND`); Ereignisausgänge `INITO`, `DONE` (Requester hat das `CNF` erhalten) und `NOTIFIED` (Requester hat ein `IND` erhalten und mit `RSP` beantwortet); Eingang `QI`, Ausgang `QO` und die Zähler `Requester_CnfCount`, `Requester_IndCount`, `Responder_ReqCount`, `Responder_IndCount`, `Responder_RspCount` (alle `UINT`).

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Requester` | `Meins::DesingPatterns::HandshakePattern::HandshakeRequester` | - | Plug `HS` (`adapter::types::bidirectional::Handshake::EVENT_HS`): sendet `REQ`/`RSP`, empfängt `CNF`/`IND`; zählt `CnfCount` und `IndCount`. |
| `Responder` | `Meins::DesingPatterns::HandshakePattern::HandshakeResponder` | - | Socket `HS`: empfängt `REQ`/`RSP`, sendet `CNF`/`IND`; zählt `ReqCount`, `IndCount`, `RspCount`. |

## Programmablauf und Verbindungen

**Adapterverbindung:** `Requester.HS` → `Responder.HS` (Requester = Plug ist Quelle, Responder = Socket ist Ziel).

**Ereignisverbindungen:**

1. `INIT` → `Requester.INIT`; `Requester.INITO` → `Responder.INIT`; `Responder.INITO` → `INITO` (Initialisierung läuft durch beide Bausteine).
2. `START` → `Requester.START`; `TRIGGER` → `Responder.TRIGGER`.
3. `Requester.DONE` → `DONE`; `Requester.NOTIFIED` → `NOTIFIED`.

**Datenverbindungen:** `QI` → `Requester.QI`; `Requester.QO` → `Responder.QI`; `Responder.QO` → `QO`; die fünf Zähler werden von den beiden Bausteinen nach außen geführt.

Beide Bausteine sind Basis-FBs im Projektordner (`HandshakeRequester.fbt`, `HandshakeResponder.fbt`).

## Zusammenfassung

Das Demo zeigt, dass der gesamte Anfrage-/Antwort-Dialog zwischen zwei Bausteinen über eine einzige Adapterverbindung läuft; Zähler machen die gesendeten und empfangenen Telegramme sichtbar.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
