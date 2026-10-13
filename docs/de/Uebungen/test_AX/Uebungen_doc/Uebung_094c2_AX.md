# Uebung_094c2_AX: Dropdown-Kette als wiederverwendbarer Baustein

![Uebung_094c2_AX_network](./Uebung_094c2_AX_network.svg)

* * * * * * * * * *

## Einleitung

Wie `Uebung_094c_AX`, aber die pro Dropdown duplizierte Lese-/Echo-Kette (`NumericValue_IDA`, `AD_TO_AUI`, `AUI_TO_AUI2`, `AUI_TO_AUDI`, `Q_NumericValue_AUDI`) wurde in den wiederverwendbaren Baustein [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) ausgelagert; `u16ObjId` ist dabei ein SubApp-Parameter statt fest verdrahtet. Weiterhin gibt es keinen gegenseitigen Ausschluss der beiden Gruppen.

## Verwendete Funktionsbausteine (FBs)

- **NumericValue_TO_AUI2_1**: Wiederverwendbarer Baustein je Dropdown
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp)
    - **Parameter**: `u16ObjId = InputNumber_I1`
    - **Erklärung**: Liest das VT-Eingabefeld `InputNumber_I1` und stellt den Index als bidirektionalen Plug `VALUEO` bereit; die Rückschreibung ins VT ist enthalten.
- **NumericValue_TO_AUI2_2**: Wiederverwendbarer Baustein je Dropdown
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp)
    - **Parameter**: `u16ObjId = InputNumber_I2`
    - **Erklärung**: Liest das VT-Eingabefeld `InputNumber_I2` und stellt den Index als bidirektionalen Plug `VALUEO` bereit; die Rückschreibung ins VT ist enthalten.
- **AOption_AUI_DEMUX_OptionalGroup_001**: Freigabe für Optionsgruppe 1
    - **Typ**: [`Option::selection::AOption_AUI_DEMUX_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_1/)
    - **Parameter**: keine
    - **Erklärung**: Gibt über `OUT1` die Optionsgruppe 1 frei (Socket `K`).
- **AOption_AUI_DEMUX_OptionalGroup_002**: Freigabe für Optionsgruppe 2
    - **Typ**: [`Option::selection::AOption_AUI_DEMUX_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_1/)
    - **Parameter**: keine
    - **Erklärung**: Gibt über `OUT1` die Optionsgruppe 2 frei (Socket `K`).
- **OptionalGroup_001**: Optionsgruppe 1
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_001`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001/) (SubApp)
    - **Parameter**: keine
    - **Erklärung**: Wird über den Adapter `OPTION` freigegeben (QI anstelle Permit).
- **OptionalGroup_002**: Optionsgruppe 2
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp)
    - **Parameter**: keine
    - **Erklärung**: Wird über den Adapter `OPTION` freigegeben (QI anstelle Permit).

## Programmablauf und Verbindungen

1. `NumericValue_TO_AUI2_1.VALUEO` → `AOption_AUI_DEMUX_OptionalGroup_001.K`, danach `AOption_AUI_DEMUX_OptionalGroup_001.OUT1` → `OptionalGroup_001.OPTION` (Dropdown 1).
2. `NumericValue_TO_AUI2_2.VALUEO` → `AOption_AUI_DEMUX_OptionalGroup_002.K`, danach `AOption_AUI_DEMUX_OptionalGroup_002.OUT1` → `OptionalGroup_002.OPTION` (Dropdown 2).

## Zusammenfassung

Die Übung ist funktional gleich zu `Uebung_094c_AX`, aber mit nur sechs statt vierzehn Instanzen deutlich kompakter.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
