# Uebung_028b2_AR: Analog-Eingang Kalibrierung mit Adaptern NVS und Hysterese-Regler am Ausgang

![Uebung_028b2_AR_network](./Uebung_028b2_AR_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung realisiert eine analoge Eingangskalibrierung mit Offset- und Skalierungsanpassung. Die Kalibrierungsparameter werden persistent im NVS (Non-Volatile Storage) gespeichert. Zusätzlich wird ein Hysterese-Regler verwendet, der auf den kalibrierten Analogwert reagiert. Die Schwellwerte und Hysteresebänder für den Regler werden ebenfalls im NVS gehalten und lassen sich über Eingabefelder im VT ändern. Der Ablauf wird durch digitale Eingänge gesteuert und über digitale Ausgänge ausgegeben.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Digitaler Eingang
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`
    - **Erklärung**: Liefert den Zustand von `I1` als AX-Adapterausgang `IN`.
- **AX_SPLIT_2**: Signalverteiler
    - **Typ**: `adapter::events::unidirectional::AX_SPLIT_2`
    - **Parameter**: keine
    - **Erklärung**: Verteilt das Signal von `DigitalInput_I1` auf zwei Wege: `OUT1` zum Digitalausgang, `OUT2` an `AnalogInput_I4.SREQ`.
- **DigitalOutput_Q1**: Digitaler Ausgang zur direkten Weitergabe des Eingangsstatus
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Schaltet `Q1` gemäß `I1`.
- **AnalogInput_I4**: Analoger Eingang mit Hysterese und Ratenbegrenzung
    - **Typ**: `logiBUS::io::AI::logiBUS_AI_IDA`
    - **Parameter**: `QI = TRUE`, `Input = AnalogInput_I4`, `AnalogInput_hysteresis = 50`, `TimeDelta = 250`, `TimeRateLimit = 100`
    - **Erklärung**: Liefert den Analogwert als Adapter `IN` (DWORD-Adapter `AD`); `SREQ` wird von `I1` über `AX_SPLIT_2` versorgt.
- **AD_TO_AUDI**: Adapterkonverter DWORD → UDINT
    - **Typ**: `adapter::conversion::unidirectional::AD_TO_AUDI`
    - **Parameter**: keine
    - **Erklärung**: Wandelt den `AD`-Adapter (DWORD) in einen `AUDI`-Adapter (UDINT) um (Bit-Umdeutung, wertegleich).
- **AUDI_TO_AR**: Adapterkonverter UDINT → REAL
    - **Typ**: `adapter::conversion::unidirectional::AUDI_TO_AR`
    - **Parameter**: keine
    - **Erklärung**: Wandelt den `AUDI`-Adapter in einen `AR`-Adapter (REAL) um. Die doppelte Konvertierung ist wichtig: ein direktes `AD_TO_AR` würde wie ein `reinterpret_cast` wirken (Kommentar im Netzwerk).
- **DigitalInput_I2_CO**: Eingang zum Auslösen der Offset-Kalibrierung
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Ein Einzelklick auf `I2` löst `CALIBRATE.CO` aus.
- **DigitalInput_I3_CS**: Eingang zum Auslösen der Skalierungskalibrierung
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Ein Einzelklick auf `I3` löst `CALIBRATE.CS` aus.
- **CALIBRATE**: Zwei-Punkt-Kalibrierung
    - **Typ**: `adapter::Engineering::measurements::AR_CALIBRATE`
    - **Parameter**: `Y_Offset = 100.0`, `Y_Scale = 600.0`
    - **Erklärung**: Berechnet `Y = (X + OFFSET) * SCALE`. `CO` setzt `OFFSET := Y_Offset - X`, `CS` setzt `SCALE := Y_Scale / (X + OFFSET)`. `CO` muss vor `CS` ausgeführt werden. `Y_Offset` und `Y_Scale` sind Zielwerte auf der Ausgangsseite.
- **NVS_OFFSET**: persistenter Speicher für den Offset
    - **Typ**: `logiBUS::storage::esp32_nvs::NVS_AR2`
    - **Parameter**: `QI = TRUE`, `SETM = TRUE`, `KEY = 'OFFSET'`, `DEFAULT_VALUE = 0.0`
    - **Erklärung**: Lädt und speichert den Offset unter dem Schlüssel `OFFSET` über den bidirektionalen Adapter `VAL` (verbunden mit `CALIBRATE.OFFSET`).
- **NVS_SCALE**: persistenter Speicher für die Skalierung
    - **Typ**: `logiBUS::storage::esp32_nvs::NVS_AR2`
    - **Parameter**: `QI = TRUE`, `SETM = TRUE`, `KEY = 'SCALE'`, `DEFAULT_VALUE = 1.0`
    - **Erklärung**: Lädt und speichert den Skalierungsfaktor unter dem Schlüssel `SCALE` (verbunden mit `CALIBRATE.SCALE`).
- **Hysteresis_AR_AX**: Hysterese-Komparator
    - **Typ**: `logiBUS::signalprocessing::hysteresis::Hysteresis_AR_AX`
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Wandelt `INPUT` (AR) in ein digitales Signal `OUTPUT` (AX): Einschalten bei `THRESHOLD + HYSTERESIS / 2`, Ausschalten bei `THRESHOLD - HYSTERESIS / 2`.
- **DigitalOutput_Q2**: Digitaler Ausgang für das Hysteresesignal
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q2`
    - **Erklärung**: Schaltet `Q2` gemäß `Hysteresis_AR_AX.OUTPUT`.

### Sub-Bausteine: `THRESHOLD` und `HYSTERESIS`

Beide sind Instanzen von `MyLib::sys::NVS_IN_AND_STORE_AR` (in `MyLib_AX`). Intern besteht der Baustein aus `NumericValue_PHYSA` (VT-Eingabefeld, liefert `rPhys`), `NVS_AR` (`QI = TRUE`, `SETM = TRUE`, `DEFAULT_VALUE = REAL#0.0`; Laden und Speichern unter `KEY`), `AR_SPLIT_2` und `Q_NumericValue_PHYSA` (schreibt den Wert zurück in das VT-Feld). Der Ausgang `VALUEO` ist ein `AR`-Adapter.

- **THRESHOLD**: `KEY = 'THRESHOLD'`, `stObj = InputNumber_THRESHOLD_N`. Liefert den Schwellwert (Mitte des Hysteresebandes) an `Hysteresis_AR_AX.THRESHOLD`.
- **HYSTERESIS**: `KEY = 'HYSTERESIS'`, `stObj = InputNumber_HYSTERESIS_N`. Liefert die volle Breite des Hysteresebandes an `Hysteresis_AR_AX.HYSTERESIS`.

## Programmablauf und Verbindungen

1. `DigitalInput_I1.IN` → `AX_SPLIT_2.IN`: `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT` (direkte Ausgabe) und `AX_SPLIT_2.OUT2` → `AnalogInput_I4.SREQ`.
2. `AnalogInput_I4.IN` → `AD_TO_AUDI.AD_IN`, `AD_TO_AUDI.AUDI_OUT` → `AUDI_TO_AR.AUDI_IN`, `AUDI_TO_AR.AR_OUT` → `CALIBRATE.X`: der Analogwert wird über die doppelte Konvertierung als REAL in die Kalibrierung gegeben.
3. `DigitalInput_I2_CO.IND` → `CALIBRATE.CO` und `DigitalInput_I3_CS.IND` → `CALIBRATE.CS`: Offset- bzw. Skalierungskalibrierung per Einzelklick.
4. `CALIBRATE.OFFSET` → `NVS_OFFSET.VAL` und `CALIBRATE.SCALE` → `NVS_SCALE.VAL`: die Kalibrierwerte werden im NVS abgelegt und geladen.
5. `CALIBRATE.Y` → `Hysteresis_AR_AX.INPUT`, `THRESHOLD.VALUEO` → `Hysteresis_AR_AX.THRESHOLD`, `HYSTERESIS.VALUEO` → `Hysteresis_AR_AX.HYSTERESIS`.
6. `Hysteresis_AR_AX.OUTPUT` → `DigitalOutput_Q2.OUT`: das Ergebnis des Komparators schaltet `Q2`.

**Lernziele**:

- Verständnis der Analogwertaufbereitung mit Adaptern und Konvertierungen in 4diac.
- Implementierung einer persistenten Kalibrierung (Offset und Skalierung) im NVS.
- Anwendung eines Hysterese-Reglers mit extern vorgebbaren Parametern.
- Steuerung des Ablaufs durch digitale Eingänge.

**Schwierigkeitsgrad**: Fortgeschritten
**Vorkenntnisse**: Grundlagen der 4diac-IDE, Umgang mit analogen Ein-/Ausgängen, einfache Adapter und NVS-Speicher.

## Zusammenfassung

Die Übung zeigt eine vollständige analoge Messkette: vom Einlesen des rohen Analogwerts über die Kalibrierung mit persistenter Speicherung bis hin zur Ausgabe über einen Hysterese-Komparator. Die Verwendung von Adaptern zur Typkonvertierung und von Sub-Bausteinen zur Wiederverwendung von NVS-Zugriffen macht den Aufbau modular und erweiterbar. Die Kalibrierung kann jederzeit über digitale Taster angepasst werden, ohne dass der Programmcode geändert werden muss.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
