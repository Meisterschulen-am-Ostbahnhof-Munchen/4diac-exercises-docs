# Uebung_094b4_AX: Optionsauswahl (2 Optionen) mit wiederverwendbarem Baustein NumericValue_TO_AUI2

![Uebung_094b4_AX_network](./Uebung_094b4_AX_network.svg)

* * * * * * * * * *

## Einleitung

Die Übung entspricht `Uebung_094b_AX`, verwendet aber den wiederverwendbaren Baustein [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) statt der inline verdrahteten Kette aus `NumericValue_IDA`, `AD_TO_AUI`, `AUI_TO_AUI2`, `AUI_TO_AUDI` und `Q_NumericValue_AUDI`. Die Objekt-ID des VT-Felds wird als SubApp-Parameter übergeben. Ausgewählt wird zwischen zwei Optionsgruppen über [`AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/) (Index, Einzelauswahl).

## Verwendete Funktionsbausteine (FBs)

- **NumericValue_TO_AUI2**: liest das VT-Eingabefeld und stellt es als bidirektionalen AUI2-Plug bereit
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp-Instanz)
    - **Parameter**: `u16ObjId = InputNumber_I1`
    - **Erklärung**: Liest das VT-Feld und reicht den Wert über `VALUEO` weiter. Der Consumer meldet den übernommenen Wert über denselben Plug zurück, das Echo wird ins VT-Feld zurückgeschrieben.
- **AOption_AUI_DEMUX_2**: Optionsauswahl mit zwei Ausgängen
    - **Typ**: [`Option::selection::AOption_AUI_DEMUX_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_2/)
    - **Parameter**: keine
    - **Erklärung**: Aktiviert abhängig vom Index `K` genau einen der Ausgänge `OUT1` oder `OUT2`.
- **OptionalGroup_001**, **OptionalGroup_002**: Optionsgruppen
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) bzw. [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp-Instanzen)
    - **Parameter**: keine
    - **Erklärung**: Gruppe 1 enthält `I1`/`Q1`, Gruppe 2 enthält `I2`/`Q2`.

## Programmablauf und Verbindungen

1. `NumericValue_TO_AUI2.VALUEO` → `AOption_AUI_DEMUX_2.K`: Der Wert des Eingabefelds wählt den aktiven Ausgang.
2. `AOption_AUI_DEMUX_2.OUT1` → `OptionalGroup_001.OPTION`, `OUT2` → `OptionalGroup_002.OPTION`.

**Verhalten**: Der Index 1 aktiviert Gruppe 1, der Index 2 aktiviert Gruppe 2; es ist immer nur eine Gruppe aktiv.

## Zusammenfassung

Die Lese-/Echo-Kette ist in [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) gekapselt. Die Übung verhält sich wie `Uebung_094b_AX` bei deutlich kleinerem Netzwerk. Die Variante mit INI-Persistenz ist `Uebung_094b4b_AX`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
