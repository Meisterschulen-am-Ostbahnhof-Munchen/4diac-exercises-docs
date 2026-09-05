# Uebung_074b_AUI: RPTO auf UT ausgeben (Adapter Version) ohne Fendt-Schaltung

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_074b_AUI`. Vereinfachte Variante von `Uebung_074a_AUI` (siehe dort für die Fendt-spezifische Ersatzwert-Schaltung): hier wird die Zapfwellendrehzahl ohne Umschaltlogik direkt angezeigt.

----

## Ziel der Übung

Anzeige der Zapfwellendrehzahl (`Rear PTO output shaft speed`) auf dem VT-Terminal, direkt vom TECU-Wert abgeleitet, ohne Fendt-spezifische Ersatzwert-Umschaltung.

-----

## Beschreibung und Komponenten

![Uebung_074b_AUI_network](./Uebung_074b_AUI_network.svg)

- **`IA_RPTO`**: `isobus::tecu::IA_RPTO`, liest die Zapfwellendrehzahl vom Tractor-ECU (TECU) über ISOBUS.
- **`CONV_AUI_AUDI`**: `adapter::conversion::unidirectional::AUI_TO_AUDI`, wandelt den TECU-Wert direkt in das für `Q_NumericValue` benötigte AUDI-Format um.
- **`Q_NumericValue_PTO`**: zeigt den Wert auf dem VT-Terminal an (`u16ObjId = NumberVariable_Rear_PTO_output_shaft_speed`).

-----

## Funktionsweise

1. `IA_RPTO` liest laufend die Zapfwellendrehzahl vom TECU.
2. Der Wert (`SPEED`) wird ohne Auswahl-/Ersatzwertlogik direkt über `CONV_AUI_AUDI` in das Anzeigeformat gewandelt.
3. Bei Initialisierung (`INITO`) wird die Anzeige vorbereitet.
