# Uebung_246_AX: logiBUS_IXA I1(Hoch)/I2(Runter) auf 3-Stufen-Sollwert 75%/25%/50%(Neutral), zwei AR_AX_SEL_AR verkettet

This article describes the 4diac IDE sub-application Uebung_246_AX (logiBUS_IXA I1(Hoch)/I2(Runter) auf 3-Stufen-Sollwert 75%/25%/50%(Neutral), zwei AR_AX_SEL_AR verkettet).

----

![Uebung_246_AX_network](./Uebung_246_AX_network.svg)

## Objective of the Exercise

The main objective of this exercise is to implement the following requirement: **logiBUS_IXA I1(Hoch)/I2(Runter) auf 3-Stufen-Sollwert 75%/25%/50%(Neutral), zwei AR_AX_SEL_AR verkettet**

-----

## Description and Components

The exercise consists of the sub-application Uebung_246_AX.SUB, which uses the following function block structure:

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
- **Q_NumericValue_PHYSA**: Instance of type isobus::UT::Q::Q_NumericValue_PHYSA.
  - Parameter stObj = OutputNumber_N3_N

### Connections and Interfaces

**Adapter Connections:**
- initval_AR_50_Neutral.OUT -> AR_AX_SEL_AR_Runter.IN0
- initval_AR_25_Runter.OUT -> AR_AX_SEL_AR_Runter.IN1
- DigitalInput_I2.IN -> AR_AX_SEL_AR_Runter.G
- AR_AX_SEL_AR_Runter.OUT -> AR_AX_SEL_AR_Hoch.IN0
- initval_AR_75_Hoch.OUT -> AR_AX_SEL_AR_Hoch.IN1
- DigitalInput_I1.IN -> AR_AX_SEL_AR_Hoch.G
- AR_AX_SEL_AR_Hoch.OUT -> Q_NumericValue_PHYSA.rPhys

### Notes from the Model

> Zwei verkettete Binaer-Selektoren statt eines 3-Wege-Selektors (den es fuer AR nicht gibt): innen waehlt I2 (Runter) zwischen Neutral(50) und Runter(25), aussen waehlt I1 (Hoch) zwischen diesem Ergebnis und Hoch(75). Werden I1 und I2 gleichzeitig gehalten, gewinnt I1 (Hoch), weil sein Selektor der aeussere ist - bewusste Prioritaet, siehe Beschreibung.

-----

## Sequence and Operation

1. **Initialization**: Upon system startup, all participating function blocks are initialized.
2. **Event and Signal Processing**: State changes at the inputs trigger events that are forwarded to downstream function blocks across the defined connections.
3. **Output Update**: The receiving function blocks process incoming events and data values to update physical and logical outputs accordingly.

-----

## Summary

Exercise Uebung_246_AX provides a clear demonstration of modular IEC 61499 application design in 4diac IDE.
