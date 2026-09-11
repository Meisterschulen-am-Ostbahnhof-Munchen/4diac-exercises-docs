# Uebung_248_AX: Wie Uebung_246_AX (Hoch/Runter auf 75/50/25%), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_248_AX (Wie Uebung_246_AX (Hoch/Runter auf 75/50/25%), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen).

----

![Uebung_248_AX_network](./Uebung_248_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Wie Uebung_246_AX (Hoch/Runter auf 75/50/25%), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_248_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **initval_AR_50_Neutral**: Instanz des Typs adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#50.0
- **initval_AR_25_Runter**: Instanz des Typs adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#25.0
- **initval_AR_75_Hoch**: Instanz des Typs adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#75.0
- **AR_AX_SEL_AR_Runter**: Instanz des Typs adapter::iec61131::selection::AR_AX_SEL_AR.
- **AR_AX_SEL_AR_Hoch**: Instanz des Typs adapter::iec61131::selection::AR_AX_SEL_AR.
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

- initval_AR_50_Neutral.OUT -> AR_AX_SEL_AR_Runter.IN0
- initval_AR_25_Runter.OUT -> AR_AX_SEL_AR_Runter.IN1
- DigitalInput_I2.IN -> AR_AX_SEL_AR_Runter.G
- AR_AX_SEL_AR_Runter.OUT -> AR_AX_SEL_AR_Hoch.IN0
- initval_AR_75_Hoch.OUT -> AR_AX_SEL_AR_Hoch.IN1
- DigitalInput_I1.IN -> AR_AX_SEL_AR_Hoch.G
- AR_AX_SEL_AR_Hoch.OUT -> AR_SPLIT_2_Anzeige_PWM.IN
- AR_SPLIT_2_Anzeige_PWM.OUT1 -> Q_NumericValue_PHYSA.rPhys
- AR_SPLIT_2_Anzeige_PWM.OUT2 -> AR_MUL_2_PWM13BIT.IN1
- initval_AR_81_91.OUT -> AR_MUL_2_PWM13BIT.IN2
- AR_MUL_2_PWM13BIT.OUT -> AR_TO_AD_NUM.AR_IN
- AR_TO_AD_NUM.AD_OUT -> DigitalOutput_Q1_PWM.OUT

### Hinweise aus dem Modell

> 0-100% Tastgrad = 0-8191 (13-Bit LEDC, siehe RampLimitFS_TO_logiBUS_QDA_PWM_OPC.SUB in MyLib_AX-1.0.0): Faktor 81,91 = 8191/100. Mit Multimeter/Oszilloskop an Q1 gegen GND: Mittelwert = Tastgrad% x Versorgungsspannung - bei 25/50/75% Tastgrad direkt vergleichbar mit den PVEA-Sollspannungen 0,25/0,50/0,75 x U_DC.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_248_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
