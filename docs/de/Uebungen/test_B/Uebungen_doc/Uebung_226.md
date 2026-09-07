# Uebung_226: Split-Bargraph

![Uebung_226_network](./Uebung_226_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung steuert zwei nebeneinanderliegende Linear-Bargraphen (`Bargraph_Split_links`/`Bargraph_Split_rechts`) so an, dass sie zusammen einen vorzeichenbehafteten Ausschlag von -42 bis +42 darstellen: bei positivem Sollwert füllt sich der rechte Balken, bei negativem der linke, der jeweils andere bleibt bei 0.

## Verwendete Funktionsbausteine (FBs)

- **Sollwert_N**: Terminal-Eingabe (Typ: `isobus::UT::io::NumericValue::NumericValue_PHYS`)
    - **Parameter**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`
    - **Erklärung**: Liest Änderungen des über `InputNumber_Sollwert` (VT-Objekt 9000) eingegebenen Werts und liefert ihn als physikalischen `REAL`-Wert über `rPhys`.
- **SplitBar**: Split-Bargraph-Ansteuerung (Typ: `isobus::UT::Q::BargraphSplitFS`)
    - **Parameter**: `stObj = Bargraph_Split_BargraphSplit`
    - **Erklärung**: Klammert den positiven Anteil des Sollwerts auf `[0, r32MaxMagnitude]` und schreibt ihn auf den rechten Balken; negiert parallel den Wert, klammert ihn ebenso und schreibt ihn auf den linken Balken. Beide Seiten schreiben direkt per Command Numeric Value (ISO 11783-6 Annex F.22) auf ihre Bargraph-Objekt-ID, ohne gebundene `NumberVariable`. Balken-IDs und der gemeinsame Magnitudenbereich kommen gebündelt aus der Konstante `Bargraph_Split_BargraphSplit` (Typ `BargraphSplit_S`), die automatisch aus der `.jop`-Geometrie erzeugt wird. `xOverRight`/`xOverLeft` melden nur eine Bereichsüberschreitung, wenn `|Sollwert| > r32MaxMagnitude` – es gibt bewusst keine `xUnder`-Ausgänge, da die jeweils inaktive Seite im Normalbetrieb ständig bei 0 liegt.

### Sub-Bausteine: keine

## Programmablauf und Verbindungen

1. Ändert der Bediener `InputNumber_Sollwert`, feuert `Sollwert_N.IND` und liefert den physikalischen Wert über `Sollwert_N.rPhys`.
2. `Sollwert_N.IND` triggert `SplitBar.REQ`.
3. `Sollwert_N.rPhys` wird direkt auf `SplitBar.rValue` geführt (kein Split-Baustein nötig, da nur ein einziger Verbraucher den Sollwert benötigt).
4. `SplitBar` berechnet daraus die beiden Balkenwerte (positiver Anteil rechts, negierter positiver Anteil links) und schreibt sie auf die beiden Bargraph-Objekte.

## Zusammenfassung

Die Übung zeigt, wie ein einzelner vorzeichenbehafteter Sollwert über den wiederverwendbaren Baustein `BargraphSplitFS` auf zwei nebeneinanderliegende Bargraphen aufgeteilt wird, ohne dass die Klammerungs- und Vorzeichenlogik jedes Mal von Hand nachgebaut werden muss. Für die vollständig über AR-Adapter verdrahtete Variante siehe `Uebung_226_AX` in `test_AX`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
