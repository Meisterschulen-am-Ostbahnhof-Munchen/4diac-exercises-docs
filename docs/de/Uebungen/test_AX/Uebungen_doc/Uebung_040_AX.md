# Uebung_040_AX: Lauflicht 8 Event gesteuert

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/041f4df4-b729-484d-b786-b6dcdf151961)

![Uebung_040_AX_network](./Uebung_040_AX_network.svg)

* * * * * * * * * *

## Einleitung

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_040_AX`. Im Gegensatz zu Übung 038 schaltet diese Schrittkette nicht automatisch weiter, sondern wartet auf Ereignisse. Ziel ist das manuelle Weiterschalten einer Schrittkette mit 8 Schritten.

Die Subapplikation `Uebung_040_AX.SUB` nutzt `sequence_E_08_loop_AX`. Hier sind die Eingänge für die Transitionen (`S1_S2`, `S2_S3`, ...) als Event-Inputs herausgeführt. Um nicht 8 Taster zu benötigen, wurde eine Logik mit Zählern (`AUI_CTU`) und Demultiplexern (`AUI_DEMUX_4`) gebaut.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: Start-Taster
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I1` → `loop.START_S1`.
- **DigitalInput_CLK_I2**: Weiterschalten der Schritte 1-4
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I2` → `E_CTU_0.CU` (Zähler hochzählen).
- **DigitalInput_CLK_I3**: Weiterschalten der Schritte 5-8
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I3` → `E_CTU_1.CU`.
- **DigitalInput_CLK_I4**: Reset-Taster
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I4` → `loop.RESET`, `E_CTU_0.R` und `E_CTU_1.R`.
- **loop**: 8-Schritt-Schrittkette (Ereignis-gesteuert, im Kreis)
    - **Typ**: `logiBUS::utils::sequence::event::sequence_E_08_loop_AX`
    - **Parameter**: keine
    - **Erklärung**: Zustände `START` und `State_01` bis `State_08`; Übergänge über die Event-Eingänge `START_S1`, `S1_S2` ... `S7_S8`, `S8_S1` sowie `RESET` (aus jedem Zustand zurück nach `START`). Ausgänge als Adapter: `STATE_NR` (AS) und `DO_S1` bis `DO_S8` (AX).
- **initval_1**: Startwert 1
    - **Typ**: `adapter::types::unidirectional::AUI::initval::initval_AUI`
    - **Parameter**: `INIT_VAL = 1`
    - **Erklärung**: Liefert beim Initialisieren den UINT-Wert 1 als Adapter `OUT`.
- **AUI_SPLIT_2**: Verteiler für den Startwert
    - **Typ**: `adapter::events::unidirectional::AUI_SPLIT_2`
    - **Parameter**: keine
    - **Erklärung**: Verteilt den Wert 1 von `initval_1` an `F_SUB_0.IN2` (`OUT1`) und `F_SUB_1.IN2` (`OUT2`).
- **E_CTU_0**, **E_CTU_1**: Ereigniszähler
    - **Typ**: `adapter::events::unidirectional::AUI_CTU`
    - **Parameter**: keine
    - **Erklärung**: Zählen bei `CU` den Zählerwert `CV` um 1 hoch, `R` setzt ihn auf 0 zurück. `E_CTU_0` wird von `I2`, `E_CTU_1` von `I3` hochgezählt.
- **F_SUB_0**, **F_SUB_1**: Subtraktion
    - **Typ**: `adapter::iec61131::arithmetic::AUI_SUB_2`
    - **Parameter**: keine
    - **Erklärung**: Bilden `CV - 1` (`IN1` = Zählerwert, `IN2` = Startwert 1), damit der erste Klick den Index 0 ergibt.
- **E_DEMUX_0**, **E_DEMUX_1**: Ereignis-Demultiplexer
    - **Typ**: `adapter::events::unidirectional::AUI_DEMUX_4`
    - **Parameter**: keine
    - **Erklärung**: Löst abhängig vom Index `K` den Ausgang `EO1` (K=0), `EO2` (K=1), `EO3` (K=2) oder `EO4` (K=3) aus.
- **AS_TO_AUDI**: Adapterkonverter SINT → UDINT
    - **Typ**: `adapter::conversion::unidirectional::AS_TO_AUDI`
    - **Parameter**: keine
    - **Erklärung**: Wandelt die Schrittnummer `loop.STATE_NR` (AS) in einen `AUDI`-Adapter.
- **Q_NumericValue_AUDI**: VT-Zahlenfeld
    - **Typ**: `isobus::UT::Q::Q_NumericValue_AUDI`
    - **Parameter**: `u16ObjId = OutputNumber_N1`
    - **Erklärung**: Zeigt die Schrittnummer im VT-Zahlenfeld `OutputNumber_N1`.
- **DigitalOutput_Q1** bis **DigitalOutput_Q8**: Digitalausgänge
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1` ... `Output_Q8`
    - **Erklärung**: `DigitalOutput_Qn` wird von `loop.DO_Sn` angesteuert.

## Programmablauf und Verbindungen

1. `DigitalInput_CLK_I1.IND` → `loop.START_S1`: Start der Kette, Schritt 1 aktiv.
2. `DigitalInput_CLK_I2.IND` → `E_CTU_0.CU`; `E_CTU_0.CV` und der Wert 1 (`AUI_SPLIT_2.OUT1`) → `F_SUB_0` → `E_DEMUX_0.K`. `E_DEMUX_0.EO1` → `loop.S1_S2`, `EO2` → `S2_S3`, `EO3` → `S3_S4`, `EO4` → `S4_S5`.
3. `DigitalInput_CLK_I3.IND` → `E_CTU_1.CU`; über `F_SUB_1` → `E_DEMUX_1.K`. `E_DEMUX_1.EO1` → `loop.S5_S6`, `EO2` → `S6_S7`, `EO3` → `S7_S8`, `EO4` → `S8_S1`.
4. `E_DEMUX_0.EO4` → `E_CTU_0.R` und `E_DEMUX_1.EO4` → `E_CTU_1.R`: jeder Zähler setzt sich nach dem vierten Klick selbst zurück.
5. `DigitalInput_CLK_I4.IND` → `loop.RESET`, `E_CTU_0.R`, `E_CTU_1.R`: Reset von Kette und beiden Zählern.
6. `loop.DO_S1` ... `loop.DO_S8` → `DigitalOutput_Q1` ... `DigitalOutput_Q8`.
7. `loop.STATE_NR` → `AS_TO_AUDI.AS_IN`, `AS_TO_AUDI.AUDI_OUT` → `Q_NumericValue_AUDI.u32NewValue`: Anzeige der Schrittnummer.

**Verhalten**:

1. Start mit `I1` → Schritt 1 aktiv.
2. Druck auf `I2` → Zählerstand 1 → Index 0 → Demux `EO1` → Event an `S1_S2` → Wechsel zu Schritt 2.
3. Druck auf `I2` → Zählerstand 2 → Index 1 → Demux `EO2` → Event an `S2_S3` → Wechsel zu Schritt 3.
4. Der vierte Druck auf `I2` löst `S4_S5` aus (Schritt 5) und setzt den Zähler zurück. Danach wird mit `I3` analog durch die Schritte 5 bis 8 und zurück zu Schritt 1 (`S8_S1`) geschaltet.

Dies simuliert eine Maschine, bei der der Bediener jeden Schritt manuell freigeben muss ("Schrittbetrieb").

## Zusammenfassung

Die Übung zeigt, wie mit zwei Zählern und zwei Demultiplexern aus nur zwei Tastern (`I2`, `I3`) acht Transitionen einer Ereignis-gesteuerten Schrittkette bedient werden können; `I1` startet, `I4` setzt zurück.

**Anwendungsbeispiel - Inbetriebnahme oder Wartung**: Der Techniker schaltet die Maschine Schritt für Schritt weiter, um zu prüfen, ob jeder Teilprozess korrekt funktioniert, bevor er auf Automatik umschaltet.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 E_CTU Event Counter Baustein auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/event-function-blocks/e_ctu/)
