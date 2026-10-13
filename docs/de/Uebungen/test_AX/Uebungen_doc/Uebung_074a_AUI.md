# Uebung_074a_AUI: RPTO auf UT ausgeben (Adapter-Version) mit Fendt-Schaltung und NOT_AVAILABLE-Filter, FIX

![Uebung_074a_AUI_network](./Uebung_074a_AUI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung gibt die Drehzahl der hinteren Zapfwelle (RPTO) des TECU auf dem ISOBUS-Terminal aus, mit der Fendt-Schaltung (bei Timeout wird 0 angezeigt) und einem zusätzlichen NOT_AVAILABLE-Filter (Version 1.1): Der Filter `AUI_FIELDBUS_UINT_TO_SIGNAL` reagiert schon auf jede einzelne Botschaft, in der ein Fehlwert `0xFFFF` steht, während die Fendt-Schaltung nur den kompletten Kommunikationsausfall (`TIMEOUT`) abdeckt.

## Verwendete Funktionsbausteine (FBs)

- **IA_RPTO**: ISOBUS-Adapter für Rear Power Take off (RPTO)
    - **Typ**: [`isobus::tecu::IA_RPTO`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/TECU/IA_RPTO/)
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Liefert die Zapfwellendrehzahl als `AUI`-Adapter `SPEED` und den Timeout-Status als `AX`-Adapter `TIMEOUT`.
- **AUI_NOT_AVAILABLE_Filter**: NOT_AVAILABLE-Filter
    - **Typ**: [`logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/fieldbus/AUI_FIELDBUS_UINT_TO_SIGNAL/)
    - **Parameter**: keine
    - **Erklärung**: Spiegelt `IN` nach `OUT`, wenn das Signal gültig ist (`IN` ≤ `VALID_SIGNAL_W`), sonst `OUT` = 0. Damit wird ein Fehlwert `0xFFFF` innerhalb einer Botschaft schon hier auf 0 gemappt.
- **initval_AUI**: Konstante für den Ersatzwert
    - **Typ**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameter**: `INIT_VAL = 0`
    - **Erklärung**: Liefert den festen Ersatzwert `0` als `AUI`-Adapterwert.
- **AUI_AX_SEL_AUI**: Auswahl (Fendt-Schaltung)
    - **Typ**: [`adapter::iec61131::selection::AUI_AX_SEL_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/selection/AUI_AX_SEL_AUI/)
    - **Parameter**: keine
    - **Erklärung**: Wählt über den Adapter `G` zwischen `IN0` (gefilterter Live-Wert) und `IN1` (Ersatzwert `0`). `G` kommt von `IA_RPTO.TIMEOUT`: Bei einem Timeout wird `0` ausgegeben.
- **CONV_AUI_AUDI**: Adapter-Konvertierung
    - **Typ**: [`adapter::conversion::unidirectional::AUI_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AUDI_AUI/AUI_TO_AUDI/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt den ausgewählten `AUI`-Wert in das für `Q_NumericValue_AUDI` benötigte `AUDI`-Format um.
- **Q_NumericValue_PTO**: Anzeige auf dem ISOBUS-Terminal
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameter**: `u16ObjId = NumberVariable_Rear_PTO_output_shaft_speed`
    - **Erklärung**: Zeigt den Wert im Objekt `NumberVariable_Rear_PTO_output_shaft_speed` an. Das Event `IA_RPTO.INITO` ist mit `Q_NumericValue_PTO.INIT` verbunden.

## Programmablauf und Verbindungen

1. `IA_RPTO.INITO` → `Q_NumericValue_PTO.INIT` (EventConnection): Nach der Initialisierung des TECU-Adapters wird die Anzeige initialisiert.
2. `IA_RPTO.SPEED` → `AUI_NOT_AVAILABLE_Filter.IN` (AdapterConnection): Die Drehzahl wird zuerst auf Gültigkeit gefiltert.
3. `AUI_NOT_AVAILABLE_Filter.OUT` → `AUI_AX_SEL_AUI.IN0` und `initval_AUI.OUT` → `AUI_AX_SEL_AUI.IN1` (AdapterConnections): Gefilterter Live-Wert und Ersatzwert `0` stehen zur Auswahl.
4. `IA_RPTO.TIMEOUT` → `AUI_AX_SEL_AUI.G` (AdapterConnection): Der Timeout-Status wählt den Ersatzwert, wenn die Kommunikation ausgefallen ist.
5. `AUI_AX_SEL_AUI.OUT` → `CONV_AUI_AUDI.AUI_IN` und `CONV_AUI_AUDI.AUDI_OUT` → `Q_NumericValue_PTO.u32NewValue` (AdapterConnections): Der ausgewählte Wert wird gewandelt und angezeigt.

**Verhalten**: Bei gültigen Botschaften zeigt das Terminal die Zapfwellendrehzahl. Enthält eine Botschaft den Fehlwert `0xFFFF`, wird `0` angezeigt; fällt die Kommunikation ganz aus (`TIMEOUT`), wird ebenfalls `0` angezeigt, statt den letzten Wert einzufrieren.

## Zusammenfassung

Die Übung kombiniert zwei Schutzmechanismen für die Anzeige von TECU-Werten: den NOT_AVAILABLE-Filter für Fehlwerte innerhalb einer Botschaft und die Fendt-Schaltung für den Kommunikationsausfall.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
