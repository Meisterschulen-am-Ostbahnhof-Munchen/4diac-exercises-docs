# Uebung_040_2_AX: Lauflicht 8

![Uebung_040_2_AX_network](./Uebung_040_2_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung ist ein Lauflicht mit acht Ausgängen (`Q1` bis `Q8`), gesteuert durch den Sequenzbaustein `sequence_E_08_loop_AX` (Instanz `loop`). `I1` startet die Sequenz in Schritt 1, `I2` und `I3` schalten die Schritte weiter, `I4` setzt zurück. Die Schrittweiterschaltung läuft über zwei Zähler (`E_CTU_0`, `E_CTU_1`), die jeweils über einen Subtrahierer (Wert aus `initval_1` = 1) und einen Demultiplexer (`AUI_DEMUX_4`) vier Übergangs-Events der Sequenz auslösen. Die aktuelle Schrittnummer wird an der Anzeige `OutputNumber_N1` ausgegeben.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**, **DigitalInput_CLK_I3**, **DigitalInput_CLK_I4**: Taster mit Klick-Erkennung
    - **Typ**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I1` / `Input_I2` / `Input_I3` / `Input_I4`, jeweils `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Melden einen Einzelklick auf `I1` bis `I4` als Event `IND`.
- **loop**: Lauflicht-Sequenz
    - **Typ**: [`logiBUS::utils::sequence::event::sequence_E_08_loop_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/utils/sequence/event/sequence_E_08_loop_AX/)
    - **Parameter**: keine
    - **Erklärung**: Acht Schritte als Ring. Die Eingänge `START_S1`, `S1_S2` bis `S8_S1` und `RESET` lösen die Übergänge aus; die Adapter `DO_S1` bis `DO_S8` zeigen den aktiven Schritt, `STATE_NR` liefert die Schrittnummer.
- **DigitalOutput_Q1** bis **DigitalOutput_Q8**: logiBUS Digitalausgänge
    - **Typ**: [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1` bis `Output_Q8`
    - **Erklärung**: Jeder Ausgang `Qn` wird von `loop.DO_Sn` angesteuert.
- **E_CTU_0**, **E_CTU_1**: Zähler
    - **Typ**: [`adapter::events::unidirectional::AUI_CTU`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_CTU/)
    - **Parameter**: keine
    - **Erklärung**: `E_CTU_0` zählt die Klicks auf `I2`, `E_CTU_1` die Klicks auf `I3` (Eingang `CU`); der Zählwert `CV` geht an den Subtrahierer.
- **initval_1**: Startwert 1
    - **Typ**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameter**: `INIT_VAL = 1`
    - **Erklärung**: Liefert den Wert 1 an `AUI_SPLIT_2`.
- **AUI_SPLIT_2**: Verteilung des Werts
    - **Typ**: [`adapter::events::unidirectional::AUI_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_SPLIT_2/)
    - **Parameter**: keine
    - **Erklärung**: `OUT1` geht an `F_SUB_0.IN2`, `OUT2` an `F_SUB_1.IN2`.
- **F_SUB_0**, **F_SUB_1**: Subtrahierer
    - **Typ**: [`adapter::iec61131::arithmetic::AUI_SUB_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/arithmetic/AUI_SUB_2/)
    - **Parameter**: keine
    - **Erklärung**: Bilden `IN1 - IN2` aus dem Zählwert `CV` (`IN1`) und dem Wert aus `initval_1` (`IN2`); das Ergebnis `OUT` geht an den Demultiplexer.
- **E_DEMUX_0**, **E_DEMUX_1**: Demultiplexer
    - **Typ**: [`adapter::events::unidirectional::AUI_DEMUX_4`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/UINT/AUI_DEMUX_4/)
    - **Parameter**: keine
    - **Erklärung**: Der Wert am Eingang `K` wählt eines der vier Events `EO1` bis `EO4`. `E_DEMUX_0` löst die Übergänge `S1_S2` bis `S4_S5` aus, `E_DEMUX_1` die Übergänge `S5_S6` bis `S8_S1`.
- **F_SINT_TO_UINT**: Konvertierung der Schrittnummer
    - **Typ**: [`adapter::conversion::unidirectional::AS_TO_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AS_AUDI/AS_TO_AUDI/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt `loop.STATE_NR` (`AS_IN`) in einen `AUDI`-Wert.
- **Q_NumericValue**: numerische Anzeige
    - **Typ**: [`isobus::UT::Q::Q_NumericValue_AUDI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/Q/Q_NumericValue_AUDI/)
    - **Parameter**: `u16ObjId = OutputNumber_N1`
    - **Erklärung**: Zeigt den Wert an `u32NewValue` an `OutputNumber_N1` an.

## Programmablauf und Verbindungen

1. `DigitalInput_CLK_I1.IND` → `loop.START_S1` (EventConnection): `I1` startet die Sequenz in Schritt 1.
2. `DigitalInput_CLK_I2.IND` → `E_CTU_0.CU` und `DigitalInput_CLK_I3.IND` → `E_CTU_1.CU` (EventConnections): Jeder Klick erhöht den jeweiligen Zähler.
3. `E_CTU_0.CV` → `F_SUB_0.IN1` und `AUI_SPLIT_2.OUT1` → `F_SUB_0.IN2`, `F_SUB_0.OUT` → `E_DEMUX_0.K` (AdapterConnections): Zählwert minus Startwert wählt den Demultiplexer-Ausgang. Dasselbe gilt für `E_CTU_1`, `F_SUB_1` (`AUI_SPLIT_2.OUT2`) und `E_DEMUX_1`; `initval_1.OUT` → `AUI_SPLIT_2.IN` speist beide.
4. `E_DEMUX_0.EO1` bis `EO4` → `loop.S1_S2`, `S2_S3`, `S3_S4`, `S4_S5` sowie `E_DEMUX_1.EO1` bis `EO4` → `loop.S5_S6`, `S6_S7`, `S7_S8`, `S8_S1` (EventConnections): Die Demultiplexer lösen die acht Schrittübergänge aus.
5. `DigitalInput_CLK_I4.IND` → `loop.RESET`, `E_CTU_0.R` und `E_CTU_1.R` (EventConnections): `I4` setzt Sequenz und beide Zähler zurück. Zusätzlich setzt `E_DEMUX_0.EO4` den Zähler `E_CTU_0` und `E_DEMUX_1.EO4` den Zähler `E_CTU_1` zurück.
6. `loop.DO_S1` bis `loop.DO_S8` → `DigitalOutput_Q1.OUT` bis `DigitalOutput_Q8.OUT` (AdapterConnections): Der aktive Schritt steuert den zugehörigen Ausgang.
7. `loop.STATE_NR` → `F_SINT_TO_UINT.AS_IN`, `F_SINT_TO_UINT.AUDI_OUT` → `Q_NumericValue.u32NewValue` (AdapterConnections): Die Schrittnummer wird an `OutputNumber_N1` angezeigt.

## Zusammenfassung

Die Übung 040_2_AX baut ein Lauflicht mit acht Schritten aus dem Sequenzbaustein `sequence_E_08_loop_AX` auf. Die Schrittübergänge werden nicht automatisch, sondern über zwei Zähler mit Subtrahierer und Demultiplexer durch Klicks auf `I2` und `I3` ausgelöst; `I1` startet, `I4` setzt zurück. Alle Verbindungen zwischen den Bausteinen laufen über Adapter.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
