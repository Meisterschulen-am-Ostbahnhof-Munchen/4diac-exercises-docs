# Uebung_266_AX: Dimmer am Terminal: wie 265, zusaetzlich Softkey F1 als zweite Bedienstelle …

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_266_AX (Dimmer am Terminal: wie 265, zusaetzlich Softkey F1 als zweite Bedienstelle (tippen = ein/aus, halten = dimmen) und Helligkeit in % auf OutputNumber_N1).

----

![Uebung_266_AX_network](./Uebung_266_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Dimmer am Terminal: wie 265, zusaetzlich Softkey F1 als zweite Bedienstelle (tippen = ein/aus, halten = dimmen) und Helligkeit in % auf OutputNumber_N1**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_266_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **Tippen_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **Lang_Start_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_LONG_PRESS_START
- **Lang_Ende_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_LONG_PRESS_UP
- **Ein_I2**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I2
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **Aus_I3**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I3
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **Halten_F1**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_STILL_HELD_START
- **Loslassen_F1**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **Gehalten**: Instanz des Typs iec61499::events::E_SR.
- **Tippen_oder_Halten**: Instanz des Typs iec61499::events::E_SWITCH.
- **Dimmer_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QD_DIMMER_G.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
  - Parameter DIM_TIME = T#4s
  - Parameter LEVEL_MIN = 10.0
  - Parameter LEVEL_MAX = 100.0
  - Parameter START = 100.0
  - Parameter SOFT = T#300ms
  - Parameter GAMMA = 2.2
- **LEVEL_TO_UDINT**: Instanz des Typs iec61131::conversion::F_REAL_TO_UDINT.
- **Helligkeit_N1**: Instanz des Typs isobus::UT::Q::Q_NumericValue.
  - Parameter u16ObjId = OutputNumber_N1

-----

## Zusammenfassung

Die Übung Uebung_266_AX demonstriert die modulare IEC 61499 Architektur in 4diac IDE.
