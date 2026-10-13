# Uebung_003f_AX: DigitalInput_I1-8 auf DigitalOutput_Q1-8

![Uebung_003f_AX_network](./Uebung_003f_AX_network.svg)

* * * * * * * * * *

## Einleitung

Die Übung schaltet alle acht digitalen Eingänge `Input_I1` bis `Input_I8` direkt auf die acht digitalen Ausgänge `Output_Q1` bis `Output_Q8` durch. Sie entspricht [Uebung_003b_AX](./Uebung_003b_AX.md), nutzt aber alle 8 Kanäle statt nur 4. Jeder Kanal besteht aus einer Instanz des wiederverwendbaren Bausteins `logiBUS_IX_TO_logiBUS_QX`; die Instanzen sind untereinander nicht verbunden.

## Verwendete Funktionsbausteine (FBs)

- **F1 ... F8**: je ein Kanal Eingang → Ausgang
    - **Typ**: [`MyLib::sys::logiBUS_IX_TO_logiBUS_QX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_B/sys/logiBUS_IX_TO_logiBUS_QX/) (SubApp-Instanz)
    - **Parameter** (je Instanz):

        | Instanz | `Input` | `Output` |
        | ------- | ------- | -------- |
        | F1 | `Input_I1` | `Output_Q1` |
        | F2 | `Input_I2` | `Output_Q2` |
        | F3 | `Input_I3` | `Output_Q3` |
        | F4 | `Input_I4` | `Output_Q4` |
        | F5 | `Input_I5` | `Output_Q5` |
        | F6 | `Input_I6` | `Output_Q6` |
        | F7 | `Input_I7` | `Output_Q7` |
        | F8 | `Input_I8` | `Output_Q8` |

    - **Erklärung**: Der Baustein enthält intern ein [`logiBUS_IX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IX/) (`QI = TRUE`) und ein [`logiBUS_QX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QX/) (`QI = TRUE`). Das Ereignis `IND` des Eingangs löst `REQ` am Ausgang aus, der Zustand wird zusätzlich per Datenverbindung von `IN` nach `OUT` übertragen. Die Parameter `Input` und `Output` wählen den physischen Kanal.

## Programmablauf und Verbindungen

1. Auf oberster Ebene gibt es keine Verbindungen zwischen den Instanzen `F1` bis `F8`; die Subapplikation hat auch keine eigene Schnittstelle.
2. In jeder Instanz wird `Input` an das interne `logiBUS_IX` und `Output` an das interne `logiBUS_QX` übergeben.
3. Intern verbunden sind `logiBUS_IX.IND` → `logiBUS_QX.REQ` (Ereignis) und `logiBUS_IX.IN` → `logiBUS_QX.OUT` (Daten).

**Verhalten**: Jeder Ausgang `Q1` ... `Q8` folgt dem Zustand des gleichnamigen Eingangs `I1` ... `I8`.

## Zusammenfassung

Die Übung zeigt, wie sich durch mehrfaches Instanziieren eines parametrierbaren Bausteins mit nur zwei Parametern je Kanal acht Eingänge auf acht Ausgänge durchschalten lassen, ohne dass Verbindungen auf der obersten Ebene nötig sind.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
