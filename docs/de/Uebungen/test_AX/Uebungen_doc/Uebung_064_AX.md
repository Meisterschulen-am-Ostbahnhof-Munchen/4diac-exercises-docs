# Uebung_064_AX: Musterschrittkette 8-Kanal ENDLOS (Adapter Version)

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_064_AX`. Wir bauen eine musterbasierte Lauflicht-Sequenz mit AX-Adaptertechnologie.

----

## Ziel der Übung

Realisierung einer Lauflicht-Sequenz über 8 Ausgänge (`Q1`-`Q8`), die im Gegensatz zu den einfachen Schrittketten (`Uebung_044_AX` bis `Uebung_063_AX`) auf einem musterbasierten Sequenzer beruht.

-----

## Beschreibung und Komponenten

Die Subapplikation `Uebung_064_AX` nutzt den Baustein `sequence_Pattern_08_08_loop_AX`. Anders als die einfachen Zustands-Sequenzer (`sequence_T_08_AX`, `sequence_E_08_AX`) adressiert dieser Baustein seine Ausgänge direkt als `Q1`-`Q8` statt über einzelne `DO_Sx`-Zustandsausgänge - er kann damit beliebige Bitmuster (nicht nur "ein Kanal nach dem anderen") über die Zeit ausgeben.

![Uebung_064_AX_network](./Uebung_064_AX_network.svg)

- **`sequence_Pattern_08_08_loop_AX`**: Musterbasierter 8-Kanal-Sequenzer, zeitgesteuert über dieselben `DT_Sx_Sy`-Parameter wie die einfachen Zeitsteuerungs-Übungen, gibt aber je Schritt ein komplettes 8-Bit-Muster aus.
- **`E_TimeOut`**: Überwacht die Sequenz als Watchdog.
- **`Q_NumericValue_AUDI`**: Zeigt die aktuelle Schrittnummer auf dem ISOBUS-Terminal an.

-----

## Funktionsweise

1. Start durch Taster `I1` -> Sequenz startet.
2. Nach jeweils `T#1s` wechselt das ausgegebene Bitmuster zum nächsten Schritt.
3. Nach dem letzten Schritt (`S8`) kehrt die Sequenz automatisch zu `S1` zurück (ENDLOS).
4. Reset durch Taster `I4` -> Alles aus.
