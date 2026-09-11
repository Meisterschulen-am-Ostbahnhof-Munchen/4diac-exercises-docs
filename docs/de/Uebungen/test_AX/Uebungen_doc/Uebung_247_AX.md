# Uebung_247_AX: Wie Uebung_245_AX (Korrekturfaktor via AR_MUL_2/initval_AR), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_247_AX (Wie Uebung_245_AX (Korrekturfaktor via AR_MUL_2/initval_AR), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen).

----

![Uebung_247_AX_network](./Uebung_247_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Wie Uebung_245_AX (Korrekturfaktor via AR_MUL_2/initval_AR), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_247_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **NumericValue_PHYSA**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_PHYSA.
  - Parameter QI = TRUE
  - Parameter stObj = InputNumber_I3_N
- **F_MUL_KORREKTUR**: Instanz des Typs adapter::iec61131::arithmetic::AR_MUL_2.
- **initval_AR_Korrekturfaktor**: Instanz des Typs adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#1.17619
- **AR_SPLIT_2_Anzeige_PWM**: Instanz des Typs adapter::events::unidirectional::AR_SPLIT_2.
- **Q_NumericValue_PHYSA**: Instanz des Typs isobus::UT::Q::Q_NumericValue_PHYSA.
  - Parameter stObj = OutputNumber_N3_N
- **AR_MUL_2_PWM13BIT**: Instanz des Typs adapter::iec61131::arithmetic::AR_MUL_2.
- **initval_AR_81_91**: Instanz des Typs adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#81.91
- **DigitalOutput_Q1_PWM**: Instanz des Typs logiBUS::io::DQ::logiBUS_QDA_PWM.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **AR_TO_AD_NUM**: Instanz des Typs adapter::conversion::unidirectional::AR_TO_AD_NUM.

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- NumericValue_PHYSA.rPhys -> F_MUL_KORREKTUR.IN1
- initval_AR_Korrekturfaktor.OUT -> F_MUL_KORREKTUR.IN2
- F_MUL_KORREKTUR.OUT -> AR_SPLIT_2_Anzeige_PWM.IN
- AR_SPLIT_2_Anzeige_PWM.OUT1 -> Q_NumericValue_PHYSA.rPhys
- AR_SPLIT_2_Anzeige_PWM.OUT2 -> AR_MUL_2_PWM13BIT.IN1
- initval_AR_81_91.OUT -> AR_MUL_2_PWM13BIT.IN2
- AR_MUL_2_PWM13BIT.OUT -> AR_TO_AD_NUM.AR_IN
- AR_TO_AD_NUM.AD_OUT -> DigitalOutput_Q1_PWM.OUT

### Hinweise aus dem Modell

> 0-100% Tastgrad = 0-8191 (13-Bit LEDC, siehe RampLimitFS_TO_logiBUS_QDA_PWM_OPC.SUB in MyLib_AX-1.0.0): Faktor 81,91 = 8191/100. Korrigierter Wert (bis 117,619% bei I3>85%) wird NICHT geklemmt - zum Nachmessen absichtlich so belassen, siehe Beschreibung. Mit Multimeter/Oszilloskop an Q1 gegen GND: Mittelwert = Tastgrad% x Versorgungsspannung.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_247_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
