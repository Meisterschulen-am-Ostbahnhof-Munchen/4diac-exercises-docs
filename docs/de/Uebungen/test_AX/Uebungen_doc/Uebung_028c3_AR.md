# Uebung_028c3_AR: Analog-Eingang Kalibrierung mit Adaptern INI und Hysterese-Regler am Ausgang und Display Anzeige

![Uebung_028c3_AR_network](./Uebung_028c3_AR_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung demonstriert die Kalibrierung eines analogen Eingangs (AnalogInput_I1) mithilfe eines Kalibrierungsadapters (AR_CALIBRATE), der einen Offset und einen Skalierungsfaktor aus einer INI-Datei lädt. Das kalibrierte Signal wird an einen Hysterese-Regler (Hysteresis_AR_AX) übergeben, der einen digitalen Ausgang (Output_Q2) schaltet. Gleichzeitig wird der kalibrierte Wert auf einem Display (Q_NumericValue_PHYSA) angezeigt. Die Übung nutzt zwei Eingänge (I3, I4) zum Triggern der Kalibrierung und einen weiteren Eingang (I2) als Freigabesignal.

## Verwendete Funktionsbausteine (FBs)

### Sub-Bausteine: Keine (alle FBs sind systemdefiniert)

- **AnalogInput_I1** (Typ: `logiBUS::io::AI::logiBUS_AI_IDA`)
    - **Verwendete interne Parameter**: `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Funktionsweise**: Liest den physikalischen Analogwert (z. B. Spannung) vom Eingang I1 aus. Die Parameter legen eine Hysterese, Abtastzeit und Ratenbegrenzung fest.
- **DigitalInput_I2** (Typ: `logiBUS::io::DI::logiBUS_IXA`)
    - **Funktionsweise**: Liest den digitalen Eingang I2. Wird als Freigabesignal für den Ausgang Q1 verwendet.
- **DigitalInput_I3_CO** (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Funktionsweise**: Ein Einzelklick an I3 erzeugt ein Ereignis an `DigitalInput_I3_CO.IND`. Die Verbindung `DigitalInput_I3_CO.IND` → `CALIBRATE.CO` löst den Kalibrier-Commit aus.
- **DigitalInput_I4_CS** (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Funktionsweise**: Ein Einzelklick an I4 erzeugt ein Ereignis an `DigitalInput_I4_CS.IND`. Die Verbindung `DigitalInput_I4_CS.IND` → `CALIBRATE.CS` startet das Lesen der Kalibrierdaten.
- **DigitalOutput_Q1** (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Funktionsweise**: Setzt den digitalen Ausgang Q1 basierend auf dem Eingang I2 (Freigabe).
- **DigitalOutput_Q2** (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Funktionsweise**: Setzt den digitalen Ausgang Q2 basierend auf dem Hysterese-Regler-Ausgang.
- **CALIBRATE** (Typ: `adapter::Engineering::measurements::AR_CALIBRATE`)
    - **Verwendete interne Parameter**: `Y_Offset = 0.0`, `Y_Scale = 100.0`
    - **Funktionsweise**: Führt eine Kalibrierung des analogen Eingangswertes durch. Berechnet einen Offset- und Skalierungsfaktor, der in die INI-Datei geschrieben wird. Eingänge: X (Rohwert), CO (Commit-Trigger), CS (Read-Trigger). Ausgänge: Y (kalibrierter Wert), OFFSET, SCALE.
- **INI_OFFSET** (Typ: `eclipse4diac::storage::INI_AR2`)
    - **Verwendete interne Parameter**: `SECTION = 'Uebung_028a_AR'`, `KEY = 'OFFSET'`, `DEFAULT_VALUE = 0.0`
    - **Funktionsweise**: Liest den Offset-Wert aus einer INI-Datei und stellt ihn als analogen Wert bereit.
- **INI_SCALE** (Typ: `eclipse4diac::storage::INI_AR2`)
    - **Verwendete interne Parameter**: `SECTION = 'Uebung_028a_AR'`, `KEY = 'SCALE'`, `DEFAULT_VALUE = 1.0`
    - **Funktionsweise**: Liest den Skalierungsfaktor aus einer INI-Datei und stellt ihn als analogen Wert bereit.
- **Hysteresis_AR_AX** (Typ: `logiBUS::signalprocessing::hysteresis::Hysteresis_AR_AX`)
    - **Verwendete interne Parameter**: `QI = TRUE`
    - **Funktionsweise**: Vergleicht den kalibrierten Eingangswert mit einem Schwellwert und einer Hysterese. Der Ausgang wird gesetzt, wenn der Wert den Schwellwert überschreitet und zurückgesetzt, wenn er unter den unteren Schwellwert (Schwellwert – Hysterese/2) fällt; der obere Schwellwert ist Schwellwert + Hysterese/2.
- **Q_NumericValue_PHYSA** (Typ: `isobus::UT::Q::Q_NumericValue_PHYSA`)
    - **Verwendete interne Parameter**: `stObj = InputNumber_PWM_DUTY_OUT_N`
    - **Funktionsweise**: Zeigt einen analogen Wert auf einem Display an (z. B. als numerischen Wert).
- **AX_SPLIT_2** (Typ: `adapter::events::unidirectional::AX_SPLIT_2`)
    - **Funktionsweise**: Verteilt ein digitales Signal (AX) auf zwei Ausgänge (OUT1, OUT2). Hier: Eingang von DigitalInput_I2 wird geteilt als Trigger für AnalogInput_I1 (OUT1) und an DigitalOutput_Q1 (OUT2).
- **AR_SPLIT_2** (Typ: `adapter::events::unidirectional::AR_SPLIT_2`)
    - **Funktionsweise**: Verteilt einen analogen Wert (AR) auf zwei Ausgänge (OUT1, OUT2). Hier: Kalibrierter Wert geht an Display und Hysterese-Regler.
- **AD_TO_AR_NUM** (Typ: `adapter::conversion::unidirectional::AD_TO_AR_NUM`)
    - **Funktionsweise**: Konvertiert einen AD-Wert (Analogwert-Adapter) numerisch (AD → UDINT → REAL) in einen AR-Wert (analoger realer Wert). Hinweis: Ein direktes AD_TO_AR würde wie ein „reinterpret_cast“ (Bitmuster) wirken.
- **AR_REAL_TO_R** (x2) (Typ: `adapter::conversion::unidirectional::AR_REAL_TO_R`)
    - **Verwendete interne Parameter**: Erstes: `OUT = 50.5`, Zweites: `OUT = 15.3`
    - **Funktionsweise**: Wandelt einen konstanten realen Wert in ein AR-Signal um. Dient als Schwellwert (THRESHOLD = 50.5) und Hysteresebreite (HYSTERESIS = 15.3) für den Hysterese-Regler.
- **INIT** (Typ: `iec61131::booleanOperators::INIT`)
    - **Funktionsweise**: Erzeugt ein einmaliges Initialisierungsereignis (INITO) beim Systemstart. Dieses Ereignis triggert die beiden AR_REAL_TO_R-Bausteine, um die konstanten Werte zu setzen.

## Programmablauf und Verbindungen

1. **Initialisierung**: Beim Start triggert der INIT-Baustein die beiden AR_REAL_TO_R-Bausteine, die die konstanten Schwellwerte (50.5 und 15.3) auf den analogen Bus legen.
2. **Analogwerterfassung**: Der AnalogInput_I1 liest den Rohwert vom Eingang I1. Dieser Rohwert wird über den Konverter AD_TO_AR_NUM numerisch in einen REAL-Wert gewandelt und an den Kalibrierungsbaustein CALIBRATE übergeben.
3. **Kalibrierung**: Die digitalen Eingänge I3 (CO) und I4 (CS) steuern die Kalibrierung:
   - Das `IND`-Ereignis von `DigitalInput_I4_CS` löst über `CALIBRATE.CS` den Lesestart aus.
   - Das `IND`-Ereignis von `DigitalInput_I3_CO` löst über `CALIBRATE.CO` den Commit der Kalibrierwerte in die INI-Datei aus (über INI_OFFSET und INI_SCALE).
   - Der kalibrierte Ausgang Y wird an den Splitter AR_SPLIT_2 weitergeleitet.
4. **Signalverteilung**:
   - AR_SPLIT_2.OUT1 leitet den kalibrierten Wert an das Display (Q_NumericValue_PHYSA) zur Anzeige.
   - AR_SPLIT_2.OUT2 leitet den kalibrierten Wert an den Hysterese-Regler (Hysteresis_AR_AX).
5. **Hysterese-Regelung**: Der Hysterese-Regler vergleicht den kalibrierten Eingangswert mit dem Schwellwert (50.5) und der Hysterese (15.3). Der Ausgang wird aktiv, wenn der Wert 50.5 + 15.3/2 erreicht oder überschreitet, und wieder inaktiv, wenn er 50.5 - 15.3/2 unterschreitet; er schaltet den digitalen Ausgang Q2.
6. **Freigabesignal**: Der digitale Eingang I2 gelangt über den Splitter AX_SPLIT_2 an den AnalogInput_I1 (OUT1, als Trigger für die Messung) und gleichzeitig an den Ausgang Q1 (OUT2, direkt weitergeleitet). Somit kann nur bei aktivem I2 der Analogwert eingelesen werden.

**Anmerkung**: Der Kommentar im Netzwerk weist darauf hin: Ein AD_TO_AR wäre wie ein „reinterpret_cast“ (Bitmuster). AD_TO_AR_NUM wandelt dagegen den Zahlenwert (AD → UDINT → REAL); nur so entsteht ein korrekter Messwert.

## Zusammenfassung

Die Übung "Uebung_028c3_AR" realisiert eine vollständige Analog-Eingangskalibrierung mit persistenter Speicherung von Offset und Skalierung in einer INI-Datei. Der kalibrierte Wert wird auf einem Display visualisiert und gleichzeitig einem Hysterese-Regler zugeführt, der einen digitalen Ausgang schaltet. Die Steuerung erfolgt über drei digitale Eingänge: Freigabe (I2), Commit (I3) und Start (I4). Die Übung vermittelt den Umgang mit Adaptern, Signalumwandlungen und der Anbindung von INI-Speicherbausteinen in 4diac.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
- [🌐 Das PWM-Signal & Infografik auf ms-muc-docs.de](https://www.ms-muc-docs.de/automatisierung/das-pwm-signal-die-kunst-spannung-zu-zerhacken/das-pwm-signal-die-kunst-spannung-zu-zerhacken-website/)
