# Uebung_094c2b_AX: Dropdown-Kette mit INI-Persistenz

![Uebung_094c2b_AX_network](./Uebung_094c2b_AX_network.svg)

* * * * * * * * * *

## Einleitung

Wie `Uebung_094c2_AX`, aber mit [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) statt [`NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/): gleicher bidirektionaler `VALUEO`-Plug, beide Indizes (K) werden zusätzlich unabhängig in `INI_AUI` persistiert (`KEY_I1_STORE`/`SECTION_I1_STORE` bzw. `KEY_I2_STORE`/`SECTION_I2_STORE`), analog `Uebung_094b3b_AX`/`Uebung_094b4b_AX`.

## Verwendete Funktionsbausteine (FBs)

- **NumericValue_TO_AUI2_AND_STORE_1**: Wiederverwendbarer Baustein je Dropdown
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) (SubApp)
    - **Parameter**: `KEY = KEY_I1_STORE`, `SECTION = SECTION_I1_STORE`, `u16ObjId = InputNumber_I1`
    - **Erklärung**: Liest das VT-Eingabefeld `InputNumber_I1` und stellt den Index als bidirektionalen Plug `VALUEO` bereit; die Rückschreibung ins VT ist enthalten. Zusätzlich wird der Index unabhängig in `INI_AUI` persistiert (Schlüssel `KEY_I1_STORE`, Sektion `SECTION_I1_STORE`).
- **NumericValue_TO_AUI2_AND_STORE_2**: Wiederverwendbarer Baustein je Dropdown
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2_AND_STORE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2_AND_STORE/) (SubApp)
    - **Parameter**: `KEY = KEY_I2_STORE`, `SECTION = SECTION_I2_STORE`, `u16ObjId = InputNumber_I2`
    - **Erklärung**: Liest das VT-Eingabefeld `InputNumber_I2` und stellt den Index als bidirektionalen Plug `VALUEO` bereit; die Rückschreibung ins VT ist enthalten. Zusätzlich wird der Index unabhängig in `INI_AUI` persistiert (Schlüssel `KEY_I2_STORE`, Sektion `SECTION_I2_STORE`).
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

1. `NumericValue_TO_AUI2_AND_STORE_1.VALUEO` → `AOption_AUI_DEMUX_OptionalGroup_001.K`, danach `AOption_AUI_DEMUX_OptionalGroup_001.OUT1` → `OptionalGroup_001.OPTION` (Dropdown 1).
2. `NumericValue_TO_AUI2_AND_STORE_2.VALUEO` → `AOption_AUI_DEMUX_OptionalGroup_002.K`, danach `AOption_AUI_DEMUX_OptionalGroup_002.OUT1` → `OptionalGroup_002.OPTION` (Dropdown 2).

## Zusammenfassung

Gegenüber `Uebung_094c2_AX` ändert sich nur der Bausteintyp und die zusätzlichen Parameter `KEY` und `SECTION`; die Verschaltung bleibt identisch.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
