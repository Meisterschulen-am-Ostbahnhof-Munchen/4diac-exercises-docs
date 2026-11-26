# Uebung_155: Stoßfreie Hand/Auto-Umschaltung CTRL_PI_AR, simulierte PT1-Strecke

This article describes the 4diac IDE sub-application Uebung_155 (Stoßfreie Hand/Auto-Umschaltung CTRL_PI_AR, simulierte PT1-Strecke).

----

![Uebung_155_network](./Uebung_155_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **Stoßfreie Hand/Auto-Umschaltung CTRL_PI_AR, simulierte PT1-Strecke**

-----

## Description and Components

The exercise consists of the sub-application Uebung_155.SUB, which uses the following function block structure:

### Instantiated Function Blocks (FBs)

- **CTRL_PI**: Instance of type OSCAT_adapter::Control::CTRL_PI_AR.
- **LIT_SET**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 50.0
- **LIT_SUP**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.0
- **LIT_OFS**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.0
- **LIT_KP**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 1.0
- **LIT_KI**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.5
- **LIT_LL**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 0.0
- **LIT_LH**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 100.0
- **E_RESTART**: Instance of type iec61499::events::E_RESTART.
- **SPERRE**: Instance of type iec61499::events::E_T_FF.
- **MAN**: Instance of type adapter::events::unidirectional::AX_T_FF.
- **Y**: Instance of type adapter::conversion::unidirectional::AR_R_TO_REAL.
- **DIFF**: Instance of type adapter::conversion::unidirectional::AR_R_TO_REAL.
- **LIM_TO_AX**: Instance of type adapter::conversion::unidirectional::AB_TO_AX.
- **LIM**: Instance of type adapter::conversion::unidirectional::AX_X_TO_BOOL.
- **DigitalInput_MAN_I1**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_RST_I2**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_SPERRE_I3**: Instance of type logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **E_CYCLE**: Instance of type iec61499::events::E_CYCLE.
  - Parameter DT = T#100ms
- **STRECKE_SPERRE**: Instance of type iec61131::selection::F_SEL.
  - Parameter IN1 = REAL#0.0
- **STRECKE_PT1**: Instance of type OSCAT::Basic::POUs::Engineering::Control::FT_PT1.
  - Parameter TM = T#5s
  - Parameter K = 1.0
- **ACT_TO_AR**: Instance of type adapter::conversion::unidirectional::AR_REAL_TO_R.
- **LIT_M_I**: Instance of type adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = 40.0

-----

## Sequence and expected behavior (from the comment in the network)

- Test stoßfreie Hand/Auto-Umschaltung (CTRL_PI_AR, OSCAT-Fix #385)
- Strecke simuliert: PT1 (5 s) aus Y, Takt 100 ms, startet von selbst.
- I1 = Hand/Auto (Start: Auto), I2 = RST Integrator, I3 = Ausgang sperren (Strecke bekommt 0).
- Handwert M_I: 40 (fest, LIT_M_I). Watch: Y.IN, STRECKE_PT1.out (Istwert), DIFF.IN, LIM.IN.
- (1) I1 Hand: Y = M_I. I1 Auto: Y springt nicht, regelt von M_I aus weiter.
- (2) Start in Auto: Y beginnt bei 50 (KP*DIFF), läuft nicht erst bis LH hoch.
- (3) Gegenprobe: in Auto I2 drücken -> Integrator 0, Y springt auf KP*DIFF.
- (4) Windup: I3 sperren -> Istwert fällt, Y läuft bis LH (100), LIM = TRUE. Lösung im Projekt: RST bei steigender Freigabe.

Note: The exercise has not been tested on hardware yet. The description comes from the sub-application and its comment, the behavior is to be read as expected.

-----

## Summary

Exercise Uebung_155 demonstrates the modular IEC 61499 architecture in 4diac IDE.
