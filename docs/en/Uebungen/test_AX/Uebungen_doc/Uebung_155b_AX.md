# Uebung_155b_AX: Stoßfreie Hand/Auto-Umschaltung CTRL_PI_AR, realer Aufbau Pumpe + Durchflussmesser

This article describes the 4diac IDE sub-application Uebung_155b_AX (Stoßfreie Hand/Auto-Umschaltung CTRL_PI_AR, realer Aufbau Pumpe + Durchflussmesser).

----

![Uebung_155b_AX_network](./Uebung_155b_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Stoßfreie Hand/Auto-Umschaltung CTRL_PI_AR, realer Aufbau Pumpe + Durchflussmesser**

-----

## Description and Components

The exercise consists of the sub-application Uebung_155b_AX.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **CTRL_PI**: Instance of type OSCAT_adapter::Control::CTRL_PI_AR.
- **LIT_SET**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 16.0
- **LIT_SUP**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.0
- **LIT_OFS**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.0
- **LIT_KP**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 10.0
- **LIT_KI**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 10.0
- **LIT_LL**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.0
- **LIT_LH**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 8184.0
- **E_RESTART**: Instance of type events::init::INIT.
- **SPERRE**: Instance of type iec61499::events::E_T_FF.
- **MAN**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **Y**: Instance of type adapter::conversion::unidirectional::AR_R_TO_REAL.
- **DIFF**: Instance of type adapter::conversion::unidirectional::AR_R_TO_REAL.
- **LIM_TO_AX**: Instance of type adapter::conversion::unidirectional::AB_TO_AX.
- **LIM**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **DigitalInput_START_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_STOP_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_CYCLE**: Instance of type iec61499::events::E_CYCLE.
  - Parameter DT = T#200ms
- **logiBUS_PI_IDA**: Instance of type logiBUS::io::PI::logiBUS_PI_IDA.
  - Parameter QI = TRUE
  - Parameter Input = PulseInput_I1
  - Parameter ImpulseDelta = 100
  - Parameter TimeDelta = 50000
  - Parameter TimeRateLimit = 10000
- **AD_TO_AR_NUM**: Instance of type adapter::conversion::unidirectional::AD_TO_AR_NUM.
- **FT_DERIV**: Instance of type OSCAT_adapter::Control::FT_DERIV_10_AR.
  - Parameter Faktor = 1.0
  - Parameter run = TRUE
- **LIT_K**: Instance of type adapter::types::unidirectional::AUI::initval::initval_AUI.
  - Parameter INIT_VAL = 9
- **ACT**: Instance of type adapter::events::unidirectional::AR_SPLIT_2_UNGATED.
- **ACT_ANZEIGE**: Instance of type adapter::conversion::unidirectional::AR_R_TO_REAL.
- **DigitalInput_MAN_I4**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_RST_I5**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I5
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_SPERRE_I6**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I6
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_MI_PLUS_I7**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I7
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_MI_MINUS_I8**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I8
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **M_I_STUFE**: Instance of type iec61499::events::E_CTUD.
  - Parameter PV = 4
- **M_I_TO_REAL**: Instance of type iec61131::conversion::F_UINT_TO_REAL.
- **M_I_MUL**: Instance of type iec61131::arithmetic::F_MUL.
  - Parameter IN2 = REAL#818.4
- **M_I_TO_AR**: Instance of type adapter::conversion::unidirectional::AR_REAL_TO_R.
- **PWM_SPERRE**: Instance of type iec61131::selection::F_SEL.
  - Parameter IN1 = REAL#0.0
- **Y_TO_AR**: Instance of type adapter::conversion::unidirectional::AR_REAL_TO_R.
- **AR_TO_AD_NUM**: Instance of type adapter::conversion::unidirectional::AR_TO_AD_NUM.
- **PWMOutput_Q1**: Instance of type logiBUS::io::DQ::logiBUS_QDA_PWM.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1

-----

## Sequence and expected behavior (from the comment in the network)

- Test stoßfreie Hand/Auto-Umschaltung (CTRL_PI_AR, OSCAT-Fix #385)
- Aufbau wie Übung 152: Durchflussmesser an I1, Pumpe an Q1. I2 = Takt starten, I3 = Takt stoppen.
- I4 = Hand/Auto (Start: Auto), I5 = RST Integrator, I6 = Pumpe sperren (PWM 0, Regler läuft weiter).
- Handwert M_I: Stufe 4 = 3273.6 (40 %); I7 +10 %, I8 -10 %. Watch: Y.IN, ACT_ANZEIGE.IN (Istwert), DIFF.IN, LIM.IN.
- (1) I4 Hand: Y = M_I. I4 Auto: Y springt nicht, regelt von M_I aus weiter.
- (2) Start in Auto: Y beginnt bei KP*DIFF (KP*DIFF), läuft nicht erst bis LH hoch.
- (3) Gegenprobe: in Auto I5 drücken -> Integrator 0, Y springt auf KP*DIFF.
- (4) Windup: I6 sperren -> Istwert fällt, Y läuft bis LH (8184), LIM = TRUE. Lösung im Projekt: RST bei steigender Freigabe.

Note: The exercise has not been tested on hardware yet. The description comes from the sub-application and its comment, the behavior is to be read as expected.

-----

## Summary

Exercise Uebung_155b_AX demonstrates the modular IEC 61499 architecture in 4diac IDE.
