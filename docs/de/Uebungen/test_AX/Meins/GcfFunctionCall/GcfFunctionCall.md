# GcfFunctionCall: Function in ST aus einer GCF aufrufen

* * * * * * * * * *

## Einleitung

Die Übung im Paket `Meins::GcfFunctionCall` zeigt, wie eine in Structured Text geschriebene Function (`.fct`) aus einer GlobalConstants-Datei (`.gcf`) heraus benutzt werden kann. Sie ist die Grundlage für eine spätere eigene Bibliothek (analog `MyLib_AX_Option`).

## Inhalt

| Datei | Zweck |
|---|---|
| `F_Double.fct` | ST-Function, `F_Double(Value) = Value * 2` (INT) |
| `const/FuncConst.gcf` | `BASE_VALUE := 7`, `DOUBLE_VALUE := F_Double(BASE_VALUE)` |
| [`GcfFunctionCallTest`](GcfFunctionCallTest.md) | Basic FB: liest `DOUBLE_VALUE` und ruft `F_Double` direkt auf, `Passed` ist TRUE, wenn beide 14 ergeben |
| [`Uebung_011d_GcfFunc_AX`](Uebung_011d_GcfFunc_AX.md) | VT-Anzeige: `DOUBLE_VALUE` als `rPhys` an `Q_NumericValue_PHYS` (`OutputNumber_N3_N`) |

## Programmablauf und Verbindungen

1. `FuncConst.gcf` importiert nichts. Die Function wird per vollem Namen `Meins::GcfFunctionCall::F_Double(...)` im `InitialValue` aufgerufen.
2. `Uebung_011d_GcfFunc_AX` importiert `DOUBLE_VALUE` und `OutputNumber_N3_N` (aus `Uebungen::const::UT::DefaultPool_Numeric`) und setzt sie als Parameter `rPhys` bzw. `stObj` des Bausteins `Q_NumericValue`.
3. Erwartung: Auf N3 erscheint 14 (Offset und Scale des Objekts wirken auf den Wert).

## Ergebnis

Der Export akzeptiert den Funktionsaufruf als `InitialValue` und wertet ihn zur Exportzeit aus. Im Bootfile `test_AX_FORTE_PC_AX.fboot` steht `rPhys` direkt als `14.0` (= `F_Double(7)`), weder `F_Double` noch `DOUBLE_VALUE` tauchen dort auf. Die Function läuft also nicht zur Laufzeit auf dem Gerät. Voraussetzung: Die Argumente sind Konstanten.

Nicht geprüft: die Anzeige 14 auf N3 im VT (Offset und Scale des Objekts).

## Hinweise

- `Comment=` in der GCF kurz halten (IDE-Wrap-Bug beim Speichern).
- `Uebung_011d.SUB` hieß wie `Uebungen/Uebung_011d_AX` und `_AUDI` (anderes Paket, gleiche Basis). Zur Vermeidung von Verwechslungen heißt sie jetzt `Uebung_011d_GcfFunc_AX`.

## Zusammenfassung

Funktionsaufrufe mit konstanten Argumenten lassen sich in einer GCF als Initialwert nutzen. Der Wert wird beim Export berechnet und landet als Konstante im Bootfile.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
