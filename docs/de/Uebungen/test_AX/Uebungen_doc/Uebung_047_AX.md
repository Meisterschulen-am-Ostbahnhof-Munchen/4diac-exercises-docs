# Uebung_047_AX: Reine Event-Schrittkette 4-Kanal (Adapter Version)

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_047_AX`. Wir bauen eine rein ereignisgesteuerte Schrittkette mit AX-Adaptertechnologie.

----

## Ziel der Übung

Realisierung einer Abfolge von 4 Ausgängen (Q1, Q2, Q3, Q4), bei der jeder Schritt ausschließlich durch ein externes Ereignis (Tasterdruck) weitergeschaltet wird - keine automatische Zeitsteuerung.

-----

## Beschreibung und Komponenten

Die Subapplikation `Uebung_047_AX` nutzt den Sequenzer `sequence_E_04_AX`, der 4 Zustände rein ereignisgesteuert durchläuft.

![Uebung_047_AX_network](./Uebung_047_AX_network.svg)

Jeder Übergang zwischen den Schritten wird durch eine eigene Tasterbetätigung (`I1` bis `I4`) ausgelöst - die Sequenz wartet an jedem Schritt, bis der nächste Taster gedrückt wird.

- **`Q_NumericValue_AUDI`**: Zeigt die aktuelle Schrittnummer auf dem ISOBUS-Terminal an.

-----

## Funktionsweise

1. Start durch Taster `I1` -> Sequenz springt zu `S1`, Ausgang `Q1` wird aktiv.
2. Jeder weitere Tasterdruck schaltet zum nächsten Schritt weiter.
3. Nach dem letzten Schritt bleibt die Sequenz stehen, bis sie zurückgesetzt wird.
4. Reset durch den letzten Taster -> Alles aus.
