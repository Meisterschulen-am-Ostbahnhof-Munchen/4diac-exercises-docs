# Uebung_010f2_AX: AuxFunction2_X1 auf DigitalOutput_Q1 mit GreenWhiteBackground mit Subapp

![Uebung_010f2_AX_network](./Uebung_010f2_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung löst den in `Uebung_010f_AX` genannten Nachteil auf, dass die Objekt-ID `AuxFunction2_X1` an zwei Stellen (Aux-FB und `GreenWhiteBackground2_AX`) einzeln eingetragen werden muss – genau wie `Uebung_010c2_AX` es für `Uebung_010c_AX` tut. Die gesamte Netzwerkstruktur besteht auf oberster Ebene nur noch aus einer einzigen SubApp-Instanz eines Bausteins aus der Bibliothek `MyLib_AX`, der die komplette Logik kapselt.

## Verwendete Funktionsbausteine (FBs)

Auf oberster Ebene der SubApp befindet sich kein einzelner FB, sondern ausschließlich die unten beschriebene SubApp-Instanz.

- **Uebung_010f2_sub_AX**: Gekapselte Logik aus Aux-Eingang, Splitter, Ausgang und Hintergrundfarbe
    - **Typ**: [`MyLib::sys::AuxIXA_TO_logiBUS_QXA_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/AuxIXA_TO_logiBUS_QXA_BG/) (SubApp-Instanz)
    - **Parameter**: `u16ObjIdA = AuxFunction2_X1` (Objekt-ID des Aux-Objekts), `Output = logiBUS_DO::Output_Q1` (Identität des physischen Ausgangs)
    - **Erklärung**: Die SubApp hat genau zwei Eingänge, `u16ObjIdA` und `Output`. Intern enthält sie vier Bausteine: `AuxFunction2_X1` (`isobus::UT::io::Auxiliary::IN::Aux_IXA`, `QI = TRUE`), `AX_SPLIT_2` (`adapter::events::unidirectional::AX_SPLIT_2`), `DigitalOutput_Q1` (`logiBUS::io::DQ::logiBUS_QXA`, `QI = TRUE`) und [`GreenWhiteBackground2_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground2_AX/) (`MyLib::sys::GreenWhiteBackground2_AX`).

Der Import `Uebungen::const::UT::DefaultPool::AuxFunction2_X1` liefert die Objekt-ID-Konstante, `logiBUS_DO` den Namensraum für die Ausgangskennung.

## Programmablauf und Verbindungen

1. Beim Instanziieren der SubApp wird `Uebung_010f2_sub_AX` mit den beiden Parametern `u16ObjIdA` (Objekt-ID des Aux-Objekts) und `Output` (Ziel-Ausgang) versehen.
2. Innerhalb der SubApp verteilen (unsichtbare) DataConnections `u16ObjIdA` an `AuxFunction2_X1.u16ObjId` und an `GreenWhiteBackground2_AX.u16ObjIdA`; eine weitere verteilt `Output` an `DigitalOutput_Q1.Output`. Die Objekt-ID muss dadurch nur noch einmal angegeben werden.
3. Der Signalfluss innerhalb der SubApp (AdapterConnections): `AuxFunction2_X1.IN` → `AX_SPLIT_2.IN`, `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT` (der physische Ausgang folgt dem Aux-Zustand) und `AX_SPLIT_2.OUT2` → `GreenWhiteBackground2_AX.DI1` (Hintergrundfarbe auf dem VT-Bildschirm und am Aux-Handle).

Die AUX-Farbe geht weiterhin sowohl an den VT-Bildschirm als auch an das Aux-Handle – das ist notwendig und wird hier nicht wegoptimiert.

## Zusammenfassung

`Uebung_010f2_AX` demonstriert, wie sich eine mehrfach benötigte Objekt-ID durch Kapselung in einen eigenen SubApp-Typ (hier der Bibliotheksbaustein `MyLib::sys::AuxIXA_TO_logiBUS_QXA_BG`) auf einen einzigen Eintragspunkt reduzieren lässt. Funktional ist die Übung identisch zu `Uebung_010f_AX`; der Unterschied liegt ausschließlich in der Wiederverwendbarkeit und Wartbarkeit der Verdrahtung.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
