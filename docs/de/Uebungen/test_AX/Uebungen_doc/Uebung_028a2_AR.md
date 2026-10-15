# Uebung_028a2_AR: Analog-Eingang Kalibrierung mit Adaptern NVS

![Uebung_028a2_AR_network](./Uebung_028a2_AR_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung realisiert eine Kalibrierung für einen analogen Eingang. Ein Analogwert wird eingelesen, über eine Adapterkonvertierung an einen Kalibrierungsbaustein übergeben und die ermittelten Offset- und Skalierungswerte werden dauerhaft im NVS-Speicher (Non-Volatile Storage) abgelegt. Zwei digitale Eingänge steuern den Kalibriermodus (Offset und Scale). Ein weiterer digitaler Eingang dient als Trigger für die analoge Abtastung und wird gleichzeitig auf einen digitalen Ausgang durchgeschleift.

## Verwendete Funktionsbausteine (FBs)

| Name | Typ | Parameter |
| ------ | ----- | ----------- |
| DigitalInput_I2 | `logiBUS::io::DI::logiBUS_IXA` | QI = TRUE, Input = Input_I2 |
| DigitalOutput_Q1 | `logiBUS::io::DQ::logiBUS_QXA` | QI = TRUE, Output = Output_Q1 |
| AnalogInput_I1 | `logiBUS::io::AI::logiBUS_AI_IDA` | QI = TRUE, Input = AnalogInput_I1, AnalogInput_hysteresis = 50, TimeDelta = 250, TimeRateLimit = 100 |
| CALIBRATE | `adapter::Engineering::measurements::AR_CALIBRATE` | Y_Offset = 100.0, Y_Scale = 600.0 |
| NVS_OFFSET | `logiBUS::storage::esp32_nvs::NVS_AR2` | QI = TRUE, SETM = TRUE, KEY = 'OFFSET', DEFAULT_VALUE = 0.0 |
| NVS_SCALE | `logiBUS::storage::esp32_nvs::NVS_AR2` | QI = TRUE, SETM = TRUE, KEY = 'SCALE', DEFAULT_VALUE = 1.0 |
| DigitalInput_I3_CO | `logiBUS::io::DI::logiBUS_IE` | QI = TRUE, Input = Input_I3, InputEvent = BUTTON_SINGLE_CLICK |
| DigitalInput_I4_CS | `logiBUS::io::DI::logiBUS_IE` | QI = TRUE, Input = Input_I4, InputEvent = BUTTON_SINGLE_CLICK |
| AX_SPLIT_2 | `adapter::events::unidirectional::AX_SPLIT_2` | (keine Parameter) |
| AD_TO_AR_NUM | `adapter::conversion::unidirectional::AD_TO_AR_NUM` | (keine Parameter) |

### Kurzbeschreibung der Bausteine

- **DigitalInput_I2**: Liest einen digitalen Eingang (Input_I2) und leitet das Ereignis über den Adapterausgang `IN` weiter.
- **DigitalOutput_Q1**: Gibt ein digitales Signal auf den Ausgang `OUT` (Output_Q1) aus, wenn der Adaptereingang `OUT` angesteuert wird.
- **AnalogInput_I1**: Analoger Eingangsbaustein. Bei einer Anfrage (SREQ) liefert er einen Analogwert. Parametriert mit Hysterese, Zeitdelta und Ratenbegrenzung.
- **CALIBRATE**: Führt die Kalibrierung durch. Empfängt den Rohwert am Adapter `X` und die Steuersignale `CO` (Calibrate Offset) und `CS` (Calibrate Scale). Gibt den berechneten Offset und die Skalierung über die Adapter `OFFSET` und `SCALE` aus.
- **NVS_OFFSET / NVS_SCALE**: Speichern einen Gleitkommawert im nichtflüchtigen Speicher unter den Keys 'OFFSET' bzw. 'SCALE' mit vorgegebenen Defaultwerten.
- **DigitalInput_I3_CO / DigitalInput_I4_CS**: Zusätzliche digitale Eingänge für die Kalibrierungssteuerung (Input_I3 = Offset-Kalibrierung, Input_I4 = Scale-Kalibrierung).
- **AX_SPLIT_2**: Verteilt ein eingehendes Adapterereignis auf zwei Ausgänge (OUT1 und OUT2).
- **AD_TO_AR_NUM**: Instanz des gleichnamigen Typs `AD_TO_AR_NUM`. Wandelt den Analogdaten-Adapter (`AD_IN`) numerisch (AD → UDINT → REAL) in einen Real-Adapter (`AR_OUT`) um. Ein einfaches `AD_TO_AR` wäre dagegen wie ein „reinterpret_cast“ (Bitmuster).

## Programmablauf und Verbindungen

Der Ablauf wird durch den digitalen Eingang `Input_I2` gestartet:

1. **Ereignisverteilung**: Das von `DigitalInput_I2` kommende Ereignis (Adapter `IN`) gelangt zu `AX_SPLIT_2`. Dieses teilt das Ereignis auf:
   - **OUT1** → verbunden mit `AnalogInput_I1.SREQ` → löst die analoge Abtastung aus.
   - **OUT2** → verbunden mit `DigitalOutput_Q1.OUT` → der digitale Ausgang `Output_Q1` wird gesetzt.

2. **Analoger Messwert**: `AnalogInput_I1` liefert nach der Abtastung einen Analogdaten-Adapter über seinen Ausgang `IN`. Dieser wird an `AD_TO_AR_NUM.AD_IN` übergeben.

3. **Konvertierung**:
   - `AD_TO_AR_NUM` wandelt den Analogdaten-Adapter (`AD_IN`) numerisch in einen Real-Adapter (`AR_OUT`) um.
   - Der Real-Adapter wird an den Kalibrierungseingang `CALIBRATE.X` weitergeleitet.

4. **Kalibrierung**: Gleichzeitig liegen an `CALIBRATE.CO` der Eingang `Input_I3` (über `DigitalInput_I3_CO`) und an `CALIBRATE.CS` der Eingang `Input_I4` (über `DigitalInput_I4_CS`) an. Je nach aktiviertem Steuersignal berechnet `CALIBRATE` den neuen Offset oder die neue Skalierung. Die Voreinstellungen (Y_Offset = 100.0, Y_Scale = 600.0) dienen als Basis.

5. **Persistente Speicherung**:
   - Der ermittelte Offset (Adapter `OFFSET`) wird an `NVS_OFFSET.VAL` übergeben und unter dem Key 'OFFSET' abgelegt.
   - Der ermittelte Skalierungsfaktor (Adapter `SCALE`) wird an `NVS_SCALE.VAL` übergeben und unter dem Key 'SCALE' abgelegt.

Die Kalibrierungswerte bleiben damit auch nach einem Neustart der Steuerung erhalten.

## Zusammenfassung

Die Übung demonstriert eine vollständige Signalkette von der digitalen Triggerung über die analoge Erfassung, Adapterkonvertierung, Kalibrierung bis zur dauerhaften Speicherung der Korrekturwerte im NVS. Sie verdeutlicht den Umgang mit mehreren Adaptertypen (Ereignis-, Daten- und Real-Adapter) und das Zusammenspiel von Standard-FBs der logiBUS-Bibliothek mit speziellen Engineering-Bausteinen. Die Adapterkonvertierung zwischen Analogdaten und Real-Werten erfolgt über den dafür vorgesehenen Baustein `AD_TO_AR_NUM` statt über ein „reinterpret_cast“.
