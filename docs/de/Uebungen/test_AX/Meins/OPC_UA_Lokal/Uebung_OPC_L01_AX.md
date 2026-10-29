# Uebung_OPC_L01_AX: Lokales I1→Q1 über OPC-UA (PUBLISH/SUBSCRIBE)

![Uebung_OPC_L01_AX_network](./Uebung_OPC_L01_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L01_AX` ist die lokale Variante von [`Training_01_OPC_UA_SUB`](../../../test_VV/03_OPC_UA/Training_01_OPC_UA_SUB/Training_01_OPC_UA_SUB.md) (test_VV): `Input_I1` schreibt per OPC-UA, `Output_Q1` liest denselben Knoten per OPC-UA. Anders als in test_VV laufen beide Seiten auf **einem** Gerät (`test_AX`, `FORTE_PC_AX`): Das Schreiben ist ein lokales `PUBLISH` (`ACTION=WRITE` ohne Endpunkt), das Lesen ein lokales `SUBSCRIBE` (`ACTION=READ` ohne Endpunkt). Die Schnittstelle der SubApp ist leer.

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `INPUT_I1` | [`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/) | `Input` = `Input_I1`, `ID` = `L01_Q1_WRITE` | Liest `Input_I1` ([`logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/)) und schreibt den Zustand per [`AX_PUBLISH_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_PUBLISH_1/) lokal in den OPC-UA-Knoten |
| `OUTPUT_Q1` | [`logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/) | `Output` = `Output_Q1`, `ID` = `L01_Q1_READ` | Liest den Knoten per [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) lokal und schaltet damit `Output_Q1` ([`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)) |

## OPC-UA-Adressraum

Alle Konstanten stehen in `const/OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`). Die IDs werden mit [`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/) (ohne Endpunkt) und [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/) (mit Endpunkt) aus je einem `*_NODE` berechnet.

| Konstante | Wert | Verwendet von |
|---|---|---|
| `L01_Q1_NODE` | `"/Objects/Lokal/L01/Q1,1:s=L01_Q1"` | Knoten (Basis für beide IDs) |
| `L01_Q1_WRITE` | `MyLib_OPC::F_OPC_LOCAL("WRITE", L01_Q1_NODE)` | `INPUT_I1` (`ID`) |
| `L01_Q1_READ` | `MyLib_OPC::F_OPC_LOCAL("READ", L01_Q1_NODE)` | `OUTPUT_Q1` (`ID`) |

## Programmablauf und Verbindungen

1. **Zwei SubApps ohne Modellverbindung**: Die `.SUB` enthält `INPUT_I1` (`x=0`) und `OUTPUT_Q1` (`x=3100`) und keine Verbindungen; die Kopplung läuft über OPC-UA.
2. **`INPUT_I1`** ([`logiBUS_IXA_TO_PUBLISH_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_PUBLISH_OPC/)): `INIT` startet `logiBUS_IXA`; dessen `INITO` löst `REQ` und `AX_PUBLISH_1.INIT` aus. `logiBUS_IXA.IN` ist per Adapterverbindung mit `AX_PUBLISH_1.IN` verbunden.
3. **`OUTPUT_Q1`** ([`logiBUS_QXA_FROM_SUBSCRIBE_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_QXA_FROM_SUBSCRIBE_OPC/)): `INIT` startet `AX_SUBSCRIBE_1`; dessen `INITO` startet `logiBUS_QXA`. `AX_SUBSCRIBE_1.OUT` ist per Adapterverbindung mit `logiBUS_QXA.OUT` verbunden.
4. **Laufzeit**: Ändert sich `Input_I1`, schreibt das lokale `PUBLISH` den neuen Wert in den Knoten; das lokale `SUBSCRIBE` liest ihn und schaltet `Output_Q1`.

## Ergebnis

Getestet auf FORTE_PC_AX.

## Lernziele

- Lokales `PUBLISH` (`WRITE`) und `SUBSCRIBE` (`READ`) ohne Endpunkt auf demselben Gerät.
- Kapselung des OPC-UA-Protokolls in je einem `MyLib::sys`-Composite pro Richtung.

**Empfohlene Reihenfolge**: [`Training_01_OPC_UA_SUB`](../../../test_VV/03_OPC_UA/Training_01_OPC_UA_SUB/Training_01_OPC_UA_SUB.md)

## Zusammenfassung

`Uebung_OPC_L01_AX` bildet das I1→Q1-Muster von Training_01 auf einem einzigen Gerät ab: lokales `PUBLISH` auf der Eingangsseite, lokales `SUBSCRIBE` auf der Ausgangsseite, beide über denselben OPC-UA-Knoten.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
