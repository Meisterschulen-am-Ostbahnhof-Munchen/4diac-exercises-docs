# Uebung_011d_GcfFunc_AX: Wert aus einer GCF-Function im VT anzeigen

![Uebung_011d_GcfFunc_AX_network](./Uebung_011d_GcfFunc_AX_network.svg)

* * * * * * * * * *

## Einleitung

Die SubApp `Uebung_011d_GcfFunc_AX` im Paket `Meins::GcfFunctionCall` zeigt den Wert `DOUBLE_VALUE` aus einer GlobalConstants-Datei auf dem Universal Terminal an. `DOUBLE_VALUE` wird in der GCF mit der ST-Function `F_Double` berechnet (siehe [GcfFunctionCall](GcfFunctionCall.md)).

## Verwendete Funktionsbausteine (FBs)

- **Q_NumericValue**: `isobus::UT::Q::Q_NumericValue_PHYS`
    - **Parameter**: `stObj = OutputNumber_N3_N`, `rPhys = DOUBLE_VALUE`
    - **Erklärung**: Gibt den physikalischen Wert an das Zahlenfeld N3 des Universal Terminals aus. Offset und Scale des Objekts wirken auf den Wert.

## Programmablauf und Verbindungen

1. Der Baustein hat keine Verbindungen. Beide Werte kommen als importierte Konstanten (`DOUBLE_VALUE` aus `Meins::GcfFunctionCall::const::FuncConst`, `OutputNumber_N3_N` aus `Uebungen::const::UT::DefaultPool_Numeric`) direkt als Parameter.
2. Erwartung: Auf N3 erscheint 14 (`F_Double(7)`).

## Zusammenfassung

Eine einzelne Konstante aus einer GCF reicht, um einen berechneten Wert im VT anzuzeigen. Die Berechnung `F_Double(7)` findet beim Export statt.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
