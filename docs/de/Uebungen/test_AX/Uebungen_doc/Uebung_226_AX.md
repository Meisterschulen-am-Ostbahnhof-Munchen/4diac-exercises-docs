# Uebung_226_AX: Split-Bargraph mit BargraphSplitFS_AR

![Uebung_226_AX_network](./Uebung_226_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung ist die Adapter-Variante von [Übung 226](../../test_B/Uebungen_doc/Uebung_226.md) (test_B): identische Funktion — ein Sollwert steuert einen geteilten Bargraphen (links/rechts) an —, aber das Lesen von `InputNumber_Sollwert` läuft über den AR-Adapter-Baustein `NumericValue_PHYSA` statt über ein plain `REQ`/`IND`-Event. Die Bargraph-Ansteuerung selbst nutzt weiterhin den wiederverwendbaren Composite-Baustein `BargraphSplitFS`, jetzt über dessen neuen Adapter-Wrapper `BargraphSplitFS_AR` angesprochen.

## Verwendete Funktionsbausteine (FBs)

- **Sollwert_N** (`isobus::UT::io::NumericValue::NumericValue_PHYSA`): AR-Adapter-Variante von `NumericValue_PHYS`.
    - **Parameter**: `QI = TRUE`, `stObj = NumberVariable_Sollwert_N`.
    - **Erklärung**: Liest den physischen Wert von `InputNumber_Sollwert` und liefert ihn als AR-Adapter-Plug `rPhys`.

### Sub-Bausteine: SplitBar (`isobus::UT::Q::BargraphSplitFS_AR`)

`BargraphSplitFS` selbst besitzt keine AR-Adapter-Schnittstelle. Genau wie `PositionMarkerFSA` (Übung 225b_AX) den Baustein `PositionMarkerFS` um einen AR-Socket herum verpackt, kapselt `BargraphSplitFS_AR` (`isobus::UT::Q`) jetzt `BargraphSplitFS` auf dieselbe Weise:

- **Parameter der Instanz `SplitBar`**: `stObj = Bargraph_Split_BargraphSplit`.
- **Interne Verdrahtung** (im `.fbt` selbst, nicht in dieser Übung geändert): Der AR-Adapter-Socket `rPhys` liefert Event (`E1`) und Datum (`D1`) direkt an eine einzige innenliegende Instanz `Inner` vom Typ `BargraphSplitFS` (`rPhys.E1 → Inner.REQ`, `rPhys.D1 → Inner.rValue`) — die Klammer-Logik für beide Balkenseiten wird also **nicht** dupliziert, sondern unverändert wiederverwendet. `Inner.xOverRight`/`Inner.xOverLeft` werden als eigene AX-Adapter-Plugs (`xOverRight`, `xOverLeft`) nach außen gereicht, exakt wie `xOver`/`xUnder` bei `PositionMarkerFSA`. `stObj` wird 1:1 an `Inner` durchgereicht.
- **Bausteindatei**: `Ventilsteuerung\4diacIDE-workspace\.lib\isobus-3.0.0\typelib\UT\Q\BargraphSplitFS_AR.fbt`.

Die bestehenden Bausteine `NumericValue_PHYSA` und `BargraphSplitFS` selbst wurden für diesen Wrapper nicht verändert.

## Programmablauf und Verbindungen

1. **Sollwert lesen**: `Sollwert_N` liest `InputNumber_Sollwert` (über `NumberVariable_Sollwert_N`) und liefert ihn als AR-Plug `rPhys`.
2. **Direkt verbinden**: `Sollwert_N.rPhys → SplitBar.rPhys`. Anders als in Übung 225b_AX (paralleles Istwert-Schreiben) braucht der Sollwert hier nur **einen** Verbraucher — deshalb entfällt `AR_SPLIT_2` vollständig, der Adapter-Socket wird direkt verbunden.
3. **Bargraph ansteuern**: Intern klammert `BargraphSplitFS_AR` den Wert wie gewohnt (gemeinsame Betragsgrenze `r32MaxMagnitude`) und steuert je nach Vorzeichen den linken oder rechten Bargraphen (`Bargraph_Split_BargraphSplit`) an; bei Überschreitung meldet es dies über `xOverRight`/`xOverLeft` (in dieser Übung nicht weiterverdrahtet).
4. Alles läuft ausschließlich über `<AdapterConnections>` — keine einzige plain Event- oder DataConnection, genau wie bei `Uebung_011b1_PHYSA`.

## Zusammenfassung

Übung 226_AX zeigt dieselbe Wrapper-Idee wie 225b_AX, diesmal für den Split-Bargraphen: `BargraphSplitFS_AR` verpackt den bewährten `BargraphSplitFS` in einen AR-Adapter-Socket, ohne dessen Klammer-Logik zu duplizieren. Weil hier nur ein einziger Verbraucher den Sollwert benötigt, entfällt der `AR_SPLIT_2`-Verteiler aus 225b_AX — die Verdrahtung wird dadurch sogar einfacher als dort. Funktional entspricht die Übung exakt [Übung 226](../../test_B/Uebungen_doc/Uebung_226.md) (test_B); nur der Verdrahtungsstil unterscheidet sich. Übung 227_AX kombiniert anschließend diese Übung mit 225b_AX, sodass ein Sollwert gleichzeitig Dreieck, Bargraph und Istwert bedient.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
