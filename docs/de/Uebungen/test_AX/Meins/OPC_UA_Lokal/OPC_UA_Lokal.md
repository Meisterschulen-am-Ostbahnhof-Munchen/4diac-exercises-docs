# OPC_UA_Lokal: OPC-UA-Beispiele auf einem Gerät

* * * * * * * * * *

## Einleitung

Die OPC-UA-Beispiele aus `test_VV/sys/03_OPC_UA` (zwei Geräte) laufen hier auf **einem** Gerät (`test_AX`, `FORTE_PC_AX`). Jede Übung ist eine SubApp. Zum Ausprobieren wählt man sie in `test_AX.sys` per „Change Type“ am SubApp `Control` aus.

## Was lokal geht (opcua.adoc)

| Aktion | FB | lokal |
|---|---|---|
| READ | SUBSCRIBE | ja |
| WRITE | PUBLISH | ja |
| CREATE_METHOD | SERVER | ja |
| CREATE/DELETE_OBJECT, CREATE/DELETE_VARIABLE | PUBLISH_0 | ja |
| CALL_METHOD | CLIENT | nein |
| SUBSCRIBE (Client-Subscription) | SUBSCRIBE | nein |

„Lokal“ heißt: ohne Endpunkt. Was lokal nicht erlaubt ist, läuft als Remote-Aufruf mit Endpunkt an die eigene Station, hier `opc.tcp://localhost:4840` (`LOCAL_OPC`). Die eigene IP funktioniert genauso; beides zählt als remote.

Die IDs stehen in `const/OpcUaLokal.gcf`, gebaut mit [`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/) / [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/) und je einem `*_NODE`.

## Übungen

| Übung | Inhalt |
|---|---|
| [`L01`](./Uebung_OPC_L01_AX.md) | wie Training_01: `Input_I1` → PUBLISH, SUBSCRIBE → `Output_Q1` (getestet auf FORTE_PC_AX) |
| [`L02`](./Uebung_OPC_L02_AX.md) | wie Training_02: Protokoll-FBs direkt (`AX_PUBLISH_1` / `AX_SUBSCRIBE_1`) |
| [`L03`](./Uebung_OPC_L03_AX.md) | wie Training_03 ohne VT: `Input_I1` ruft die Toggle-Methode per localhost, Q1 Flipflop, Q2 Zustand (getestet auf FORTE_PC_AX) |
| [`L04`](./Uebung_OPC_L04_AX.md)–[`L09`](./Uebung_OPC_L09_AX.md) | wie Training_04–09 (SoftKeys, SR/Toggle, Adapter-Varianten), Methoden per localhost |
| [`L10`](./Uebung_OPC_L10_AX.md)–[`L12`](./Uebung_OPC_L12_AX.md) | wie Training_10–12 (Hoch/Runter mit ILOCK, Rückmeldung, gebündelt) |
| [`L13`](./Uebung_OPC_L13_AX.md) | Flip-Flop (opcua.adoc): Q1 = NICHT I1 über Flip/Flop-Knoten |
| [`L14`](./Uebung_OPC_L14_AX.md) | Adder (opcua.adoc): Num1 + Num2 = Result, Werte per UaExpert |
| [`L15`](./Uebung_OPC_L15_AX.md) | Methode AddValues: I1 ruft per CLIENT_2_1 mit 43 und 10 auf |
| [`L16`](./Uebung_OPC_L16_AX.md) | Variablen per CLIENT: I1 schreibt 42 auf Increment, liest Incremented = 43 |
| [`L17`](./Uebung_OPC_L17_AX.md) | Client-Subscription: wie L16, plus SUBSCRIBE mit Endpunkt |
| [`L18`](./Uebung_OPC_L18_AX.md) | PUBLISH_0: I1/I2 legen Objekt/Variable an, I3/I4 löschen sie |
| [`L19`](./Uebung_OPC_L19_AX.md) | lokal 2x lesen: zwei SUBSCRIBE auf einen Knoten (Q1 und Q2) (getestet auf FORTE_PC_AX) |

L19 braucht eine FORTE mit eclipse-4diac/4diac-forte#1082. Ohne diese Änderung meldet die zweite SUBSCRIBE „Cannot add another one“ (siehe 4diac-documentation#123).

Die Doku-Beispiele L13–L18 gibt es auch auf zwei Geräten als `test_VV/sys/03_OPC_UA/Training_13` bis `_18`.

## Ergebnis

Auf `FORTE_PC_AX` getestet sind L01, L03 (2026-10-18) und L19. Alle anderen Übungen sind nicht auf dem Gerät getestet.

## Regel: jeder SERVER braucht IND → RSP

Jeder `SERVER_0` (allgemein jeder SERVER-FB, der eine Methode anlegt) braucht eine Verbindung `IND` → `RSP` direkt am eigenen Baustein. Ohne `RSP` wartet FORTE in `onServerMethodCall` bis zu 4 Sekunden auf die Antwort und hält dabei die globale Service-Sperre von open62541. In dieser Zeit stocken **alle** OPC-UA-Publishes der Station, nicht nur die eine Methode.

Die eigentliche Reaktion auf den Aufruf hängt als zweite, unabhängige Verbindung am selben `IND`. `RSP` wird dadurch nie verzögert. Bei Methoden mit Rückgabewert (L15: `SERVER_1_2`) wird `RSP` erst nach der Berechnung ausgelöst. Die Berechnung muss deshalb sofort fertig sein und darf nicht auf ein weiteres Ereignis warten.

Die Regel steht auch bei [SERVER_0](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/net/SERVER_0/) in der Bibliotheks-Referenz.

## Bausteine

- Lokale Varianten (`_LOC`, PUBLISH statt Remote-WRITE) in `MyLib_AX/typelib/sys`, z. B. [`Toggle_RPC_FROM_Remote_QXA_OPC_LOC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Toggle_RPC_FROM_Remote_QXA_OPC_LOC/), [`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/).
- Lokale Net-FBs in `MyLib_AX/typelib/net`: `*_SERVER_0_PUBLISH_1` (z. B. [`ASRT_AX_SERVER_0_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/ASRT_AX_SERVER_0_PUBLISH_1/)), [`A2X2_PUBLISH_2_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/A2X2_PUBLISH_2_SUBSCRIBE_2/), [`A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/net/A2X2_PUBLISH_2_SUBSCRIBE_2_PLUG/).
- Taster statt VT: [`Input_Toggle_RPC_TO_Remote_QXA_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_Toggle_RPC_TO_Remote_QXA_OPC/), [`Input_Toggle_RPC_TO_Remote_AX_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_Toggle_RPC_TO_Remote_AX_OPC/), [`Input_CLIENT_Method_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Input_CLIENT_Method_IE/).

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
