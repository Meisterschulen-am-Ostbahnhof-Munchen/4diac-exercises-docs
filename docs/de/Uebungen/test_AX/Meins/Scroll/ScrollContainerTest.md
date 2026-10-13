# ScrollContainerTest: Test für Container-Scrollen

![ScrollContainerTest_network](./ScrollContainerTest_network.svg)

* * * * * * * * * *

## Einleitung

`ScrollContainerTest` ist ein Test für das Scrollen eines VT-Containers (Kommentar im Typ: "Test für Container Scrollen."). Importiert wird die Pool-Konstante `Container_Scroll` aus `DefaultPool_Scroll`. Die SubApp enthält einen Scroll-Baustein für die Softkey-Bedienung sowie eine direkte Durchschaltung von Eingang I1 auf Ausgang Q1.

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `DigitalInput_I1` | [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input` = `Input_I1`, `QI` = `TRUE` | Liest den digitalen Eingang I1. |
| `DigitalOutput_Q1` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q1`, `QI` = `TRUE` | Schreibt den digitalen Ausgang Q1. |
| `ScrollFS_PHYS` | [`isobus::UT::Q::ScrollFS_PHYS_Softkey`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/ScrollFS_PHYS_Softkey/) | `stObj` = `Container_Scroll`, `xScale` = `TRUE` | Scrollbare VT-Liste mit Softkey-Bedienung für den Container `Container_Scroll`. |

## Programmablauf und Verbindungen

Das Netzwerk enthält genau eine Verbindung:

1. `DigitalInput_I1.IN` → `DigitalOutput_Q1.OUT` (Adapter): der Eingang I1 wird direkt auf den Ausgang Q1 gelegt.

`ScrollFS_PHYS` ist im Netzwerk nicht verschaltet; er wird nur über seine Parameter (`stObj`, `xScale`) konfiguriert.

## Zusammenfassung

`ScrollContainerTest` ist ein kleiner Testaufbau: ein Scroll-Baustein für den Container `Container_Scroll` plus eine Eins-zu-eins-Durchschaltung von I1 nach Q1.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
