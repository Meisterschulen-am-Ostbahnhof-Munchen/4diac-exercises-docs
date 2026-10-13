# Uebung_094b3b_AX: Optionsauswahl mit INI-persistiertem Index (NumericValue_TO_AUI2_AND_STORE)

![Uebung_094b3b_AX_network](./Uebung_094b3b_AX_network.svg)

* * * * * * * * * *

## Einleitung

Die Übung entspricht `Uebung_094b3_AX`, verwendet aber [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) statt [`NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/). Der Plug `VALUEO` ist derselbe, zusätzlich wird der Index `K` in einer INI-Datei persistiert. Beim Start gewinnt der gespeicherte Wert per `AUI_LAST_2` gegen die Live-Lesung des VT-Felds. Die Konstanten `KEY_I1_STORE` und `SECTION_I1_STORE` sind bereits aus `Uebung_012c_AUDI` bekannt.

## Verwendete Funktionsbausteine (FBs)

- **NumericValue_TO_AUI2_AND_STORE_1**: VT-Eingabefeld lesen, als AUI2-Plug bereitstellen und in INI speichern
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) (SubApp-Instanz)
    - **Parameter**: `KEY = KEY_I1_STORE`, `SECTION = SECTION_I1_STORE`, `u16ObjId = InputNumber_I1`
    - **Erklärung**: Wie `NumericValue_TO_AUI2`, mit zusätzlicher INI-Persistenz: Beim Boot gewinnt der aus der INI geladene Wert (per `AUI_LAST_2` gegen die Live-VT-Lesung gemergt), danach jede Live-Änderung.
- **AOption_AUI_DEMUX_3**: Optionsauswahl mit drei Ausgängen
    - **Typ**: [`Option::selection::AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/)
    - **Parameter**: keine
    - **Erklärung**: Aktiviert abhängig vom Index `K` genau einen der Ausgänge `OUT1`, `OUT2`, `OUT3`.
- **OptionalGroup_001**, **OptionalGroup_002**: echte Optionsgruppen
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) bzw. [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp-Instanzen)
    - **Parameter**: keine
    - **Erklärung**: Gruppe 1 enthält `I1`/`Q1`, Gruppe 2 enthält `I2`/`Q2`.
- **DummyGroup**: neutraler Platzhalter-Slot
    - **Typ**: [`MyLib_AX_Option::sys::DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) (SubApp-Instanz)
    - **Parameter**: keine
    - **Erklärung**: Ohne reale I/O und ohne Wirkung; Zustand "nichts ausgewählt".

## Programmablauf und Verbindungen

1. `NumericValue_TO_AUI2_AND_STORE_1.VALUEO` → `AOption_AUI_DEMUX_3.K`: Der (ggf. aus der INI geladene) Index wählt den aktiven Ausgang.
2. `AOption_AUI_DEMUX_3.OUT1` → `DummyGroup.OPTION`, `OUT2` → `OptionalGroup_001.OPTION`, `OUT3` → `OptionalGroup_002.OPTION`.

**Verhalten**: Wie in `Uebung_094b3_AX` (1 = nichts ausgewählt, 2 = Gruppe 1, 3 = Gruppe 2), zusätzlich bleibt die Auswahl über einen Neustart erhalten.

## Zusammenfassung

Der Austausch des Bausteins durch die `_AND_STORE`-Variante macht die Optionsauswahl neustartfest, ohne dass sich das restliche Netzwerk ändert.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
