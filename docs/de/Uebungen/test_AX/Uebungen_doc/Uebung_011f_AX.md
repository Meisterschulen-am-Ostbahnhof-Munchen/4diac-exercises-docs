# Uebung_011f_AX: Numeric Value Input I3 durchschleifen auf N3 (Input und Output PHYS via NumericObjectPool_S)

![Uebung_011f_AX_network](./Uebung_011f_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung schleift einen numerischen Eingabewert des VT (Eingabefeld `I3`) unverändert auf ein numerisches Ausgabefeld (`N3`) durch. Eingang und Ausgang sind dabei jeweils als physikalischer `REAL`-Wert (`PHYS`) ausgeführt: Die Umrechnung zwischen Rohwert und physikalischem Wert (Skalierung, Offset, Dezimalstellen) steckt in den Parametern vom Typ `NumericObjectPool_S` (`stObj`), die aus der Pool-Konstantendatei importiert werden. Die SubApp hat keine eigene Schnittstelle.

## Verwendete Funktionsbausteine (FBs)

- **NumericValue_PHYS**: Numerischer VT-Eingabewert als physikalischer Wert
    - **Typ**: [`isobus::UT::io::NumericValue::NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/NumericValue/NumericValue_PHYSA/)
    - **Parameter**: `QI = TRUE`, `stObj = InputNumber_I3_N`
    - **Erklärung**: Liefert den im VT-Eingabefeld `I3` eingegebenen Wert als physikalischen `REAL`-Wert über den Adapter-Ausgang `rPhys` (AR-Adapter).
- **Q_NumericValue_PHYS**: Numerischer VT-Ausgabewert aus einem physikalischen Wert
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_PHYSA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_PHYSA/)
    - **Parameter**: `stObj = OutputNumber_N3_N`
    - **Erklärung**: Empfängt einen physikalischen `REAL`-Wert über den Adapter `rPhys` und schreibt ihn in das numerische Ausgabeobjekt `N3` des VT.

Die Konstanten `InputNumber_I3_N` und `OutputNumber_N3_N` stammen aus `Uebungen::const::UT::DefaultPool_Numeric`.

## Programmablauf und Verbindungen

1. `NumericValue_PHYS.rPhys` → `Q_NumericValue_PHYS.rPhys` (AdapterConnection): der physikalische Wert der Eingabe `I3` wird direkt an die Ausgabe `N3` weitergegeben.

**Beispiel** (Kommentar im Modell): Eine Eingabe von -500.00 in `I3` ergibt `rPhys = -500.0`; `Q_NumericValue_PHYS` (`N3`) zeigt anschließend -500.00 an.

## Zusammenfassung

Die Übung `Uebung_011f_AX` zeigt die einfachste Verwendung der PHYS-Varianten für numerische VT-Objekte: Ein Eingabe-FB und ein Ausgabe-FB werden über eine einzige AR-Adapterverbindung gekoppelt, die Skalierung und der Offset stecken vollständig in den `NumericObjectPool_S`-Parametern.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
