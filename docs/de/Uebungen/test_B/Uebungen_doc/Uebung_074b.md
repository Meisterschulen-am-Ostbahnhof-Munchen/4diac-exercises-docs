# Uebung_074b: RPTO auf UT ausgeben ohne Fendt-Schaltung

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_074b`. Direkte (nicht-Adapter-) Variante von `Uebung_074b_AUI` - siehe dort für die konzeptionell identische Funktionsweise.

----

## Ziel der Übung

Anzeige der Zapfwellendrehzahl (`Rear PTO output shaft speed`) auf dem VT-Terminal, direkt vom TECU-Wert abgeleitet, ohne Umschaltlogik.

-----

## Beschreibung und Komponenten

![Uebung_074b_network](./Uebung_074b_network.svg)

- **`I_RPTO`**: `isobus::tecu::I_RPTO`, liest die Zapfwellendrehzahl vom Tractor-ECU (TECU) über ISOBUS.
- **`Q_NumericValue_GBSD`**: zeigt den Wert direkt auf dem VT-Terminal an (`u16ObjId = NumberVariable_Rear_PTO_output_shaft_speed`).

-----

## Funktionsweise

1. `I_RPTO` liest laufend die Zapfwellendrehzahl vom TECU.
2. Bei jeder Meldung (`IND`) wird der Wert (`REAR_PTO_OUTP_SHAFT_SPEED`) direkt in `Q_NumericValue_GBSD` angezeigt - ohne Adapter- oder Auswahllogik dazwischen.
