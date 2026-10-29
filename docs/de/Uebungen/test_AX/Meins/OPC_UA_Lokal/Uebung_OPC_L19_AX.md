# Uebung_OPC_L19_AX: Lokal zweimal lesen: zwei SUBSCRIBE auf einen Knoten

![Uebung_OPC_L19_AX_network](./Uebung_OPC_L19_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L19_AX` zeigt, dass zwei lokale Leser denselben OPC-UA-Knoten lesen können: `Input_I1` schreibt per PUBLISH (ohne Endpunkt) den Knoten `Q`, `Output_Q1` und `Output_Q2` lesen ihn beide per SUBSCRIBE (gleiche ID `L19_Q_READ`). Die Übung enthält keine Event- oder Datenverbindungen; die Kopplung läuft allein über den OPC-UA-Knoten.

Übersicht der Serie: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

Kommentar im `.SUB`: „Lokal 2x lesen: Input_I1 schreibt per PUBLISH, Output_Q1 und Output_Q2 lesen denselben Knoten (braucht FORTE mit 4diac-forte#1082)“

## Verwendete Funktionsbausteine (FBs)

### `INPUT_I1`

- Typ: [`MyLib::sys::logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/)
- Parameter:

    - `Input` = `Input_I1`
    - `ID` = `L19_Q_WRITE`

- Erklärung: Schreibt den Zustand von `Input_I1` lokal (ohne Endpunkt, `WRITE`) per PUBLISH auf den Knoten `Q` (BOOL).

### `OUTPUT_Q1`

- Typ: [`MyLib::sys::logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/)
- Parameter:

    - `Output` = `Output_Q1`
    - `ID` = `L19_Q_READ`

- Erklärung: Liest den Knoten `Q` lokal (`READ`, SUBSCRIBE) und steuert damit `Output_Q1`.

### `OUTPUT_Q2`

- Typ: [`MyLib::sys::logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/)
- Parameter:

    - `Output` = `Output_Q2`
    - `ID` = `L19_Q_READ`

- Erklärung: Liest denselben Knoten `Q` ein zweites Mal (gleiche ID `L19_Q_READ`) und steuert damit `Output_Q2`.

## OPC-UA-Adressen (`OpcUaLokal.gcf`)

Die IDs stammen aus `const/OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`).

| Konstante | Aktion | Knoten / ID |
|---|---|---|
| `L19_Q_WRITE` | WRITE (lokal) | `/Objects/Lokal/L19/Q,1:s=L19_Q` |
| `L19_Q_READ` | READ (lokal) | `/Objects/Lokal/L19/Q,1:s=L19_Q` |

## Programmablauf und Verbindungen

Die `.SUB` enthält keine Event- oder Datenverbindungen.

1. `INPUT_I1` (`logiBUS_IXA_TO_PUBLISH_OPC`) schreibt den Zustand von `Input_I1` mit `ID` = `L19_Q_WRITE` auf den Knoten `Q`.
2. `OUTPUT_Q1` (`logiBUS_QXA_FROM_SUBSCRIBE_OPC`) liest `Q` mit `ID` = `L19_Q_READ` und steuert `Output_Q1`.
3. `OUTPUT_Q2` (`logiBUS_QXA_FROM_SUBSCRIBE_OPC`) liest `Q` mit derselben `ID` = `L19_Q_READ` und steuert `Output_Q2`.

## Hinweis / Ergebnis

Getestet auf FORTE_PC_AX. Voraussetzung: Die FORTE braucht [eclipse-4diac/4diac-forte#1082](https://github.com/eclipse-4diac/4diac-forte/pull/1082) (mehrere lokale Leser je Knoten). Ohne diese Änderung meldet die zweite SUBSCRIBE „Cannot add another one“ (siehe [4diac-documentation#123](https://github.com/eclipse-4diac/4diac-documentation/issues/123)).

## Lernziele

- Lokales Schreiben (`WRITE`) und mehrfaches lokales Lesen (`READ`) desselben Knotens
- Wiederverwendung der Bibliotheks-SubApps `logiBUS_IXA_TO_PUBLISH_OPC` und `logiBUS_QXA_FROM_SUBSCRIBE_OPC`
- Kopplung zweier Ausgänge über einen gemeinsamen OPC-UA-Knoten statt über Verbindungen

## Zusammenfassung

Die Übung zeigt, dass mit der passenden FORTE ein Knoten lokal von mehreren SUBSCRIBE gelesen werden kann.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
