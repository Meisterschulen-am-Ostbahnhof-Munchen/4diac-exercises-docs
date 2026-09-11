# Uebung_248_AX: Wie Uebung_246_AX (Hoch/Runter auf 75/50/25%), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen

This article describes the 4diac IDE sub-application Uebung_248_AX (Wie Uebung_246_AX (Hoch/Runter auf 75/50/25%), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen).

----

![Uebung_248_AX_network](./Uebung_248_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Wie Uebung_246_AX (Hoch/Runter auf 75/50/25%), zusaetzlich echter logiBUS_QDA_PWM-Ausgang Q1 zum Nachmessen**

-----

## Description and Components

The exercise consists of the sub-application Uebung_248_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **DigitalInput_I1**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
- **DigitalInput_I2**: Instance of type logiBUS::io::DI::logiBUS_IXA.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
- **initval_AR_50_Neutral**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#50.0
- **initval_AR_25_Runter**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#25.0
- **initval_AR_75_Hoch**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#75.0
- **AR_AX_SEL_AR_Runter**: Instance of type adapter::iec61131::selection::AR_AX_SEL_AR.
- **AR_AX_SEL_AR_Hoch**: Instance of type adapter::iec61131::selection::AR_AX_SEL_AR.
- **AR_SPLIT_2_Anzeige_PWM**: Instance of type adapter::events::unidirectional::AR_SPLIT_2.
- **Q_NumericValue_PHYSA**: Instance of type isobus::UT::Q::Q_NumericValue_PHYSA.
  - Parameter stObj = OutputNumber_N3_N
- **AR_MUL_2_PWM13BIT**: Instance of type adapter::iec61131::arithmetic::AR_MUL_2.
- **initval_AR_81_91**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#81.91
- **DigitalOutput_Q1_PWM**: Instance of type logiBUS::io::DQ::logiBUS_QDA_PWM.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **AR_TO_AD_NUM**: Instance of type adapter::conversion::unidirectional::AR_TO_AD_NUM.

### Connections and Interfaces

**Adapter Connections:**

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

### Notes from the Model

> 0-100% Tastgrad = 0-8191 (13-Bit LEDC, siehe RampLimitFS_TO_logiBUS_QDA_PWM_OPC.SUB in MyLib_AX-1.0.0): Faktor 81,91 = 8191/100. Mit Multimeter/Oszilloskop an Q1 gegen GND: Mittelwert = Tastgrad% x Versorgungsspannung - bei 25/50/75% Tastgrad direkt vergleichbar mit den PVEA-Sollspannungen 0,25/0,50/0,75 x U_DC.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_248_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
