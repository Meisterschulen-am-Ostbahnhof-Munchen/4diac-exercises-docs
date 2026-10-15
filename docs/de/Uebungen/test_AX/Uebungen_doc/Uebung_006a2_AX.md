# Uebung_006a2_AX: 2x SR und T-Flip-Flop mit IE

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/041f4df4-b729-484d-b786-b6dcdf151961)

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_006a2_AX`.

----

## Ziel der Übung

Demonstration einer Zentral-Aus-Funktion.

-----

## Beschreibung und Komponenten

Die Subapplikation `Uebung_006a2_AX.SUB` steuert zwei unabhängige Lampen, die gemeinsam gelöscht werden können.

### Funktionsbausteine (FBs)

![Uebung_006a2_AX_network](./Uebung_006a2_AX_network.svg)

  - **`I1`**: Reset für beide.
  - **`I2`**: Toggelt Lampe 1.
  - **`I3`**: Toggelt Lampe 2.
  - **2x `AX_T_FF_SR`**: Je einer pro Lampe.

-----

## Funktionsweise

-   `I2` ist mit `CLK` von FF1 verbunden.
-   `I3` ist mit `CLK` von FF2 verbunden.
-   `I1` ist mit `R` von **beiden** Flip-Flops verbunden (Fan-Out).

Ein Druck auf `I1` schaltet sofort beide Lampen aus, egal in welchem Zustand sie waren.

-----

## Anwendungsbeispiel

**Bürobeleuchtung**: Jeder Schreibtisch hat sein eigenes Licht (`I2`, `I3`), aber am Ausgang gibt es einen Schalter "Raum verlassen", der alles ausschaltet (`I1`).
