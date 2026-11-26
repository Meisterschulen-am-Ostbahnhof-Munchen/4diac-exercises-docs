# Uebung_267_AX: FIFO mit Adapter: Tastendruecke puffern, Q1 blinkt je Wert einmal …

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_267_AX (FIFO mit Adapter: Tastendruecke puffern, Q1 blinkt je Wert einmal (PROCESSED-Handshake)).

----

![Uebung_267_AX_network](./Uebung_267_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **FIFO mit Adapter: Tastendruecke puffern, Q1 blinkt je Wert einmal (PROCESSED-Handshake)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_267_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **BUF_10**: Instanz des Typs eclipse4diac::convert::providers::PROVIDE_ARR_0010_UINT.
- **CAPACITY**: Instanz des Typs eclipse4diac::utils::arrays::F_LEN_ARRAY.
- **FIFO**: Instanz des Typs FIFO_adapter::FIFO_UINT_Adapter.
  - Parameter QI = TRUE
  - Parameter IN = 1
- **CONSUMER**: Instanz des Typs FIFO_adapter::AFIFO_UINT_TO_E.
- **DigitalInput_CLK_I1**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I1
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **DigitalInput_CLK_I4**: Instanz des Typs logiBUS::io::DI::logiBUS_IE.
  - Parameter QI = TRUE
  - Parameter Input = Input_I4
  - Parameter InputEvent = BUTTON_SINGLE_CLICK
- **LED**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **ON_TIME**: Instanz des Typs iec61499::events::E_DELAY.
  - Parameter DT = T#1s
- **PAUSE**: Instanz des Typs iec61499::events::E_DELAY.
  - Parameter DT = T#500ms
- **DigitalOutput_Q1**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q1
- **PUSH_OK**: Instanz des Typs iec61499::events::E_SWITCH.
- **FULL**: Instanz des Typs adapter::events::unidirectional::AX_SR.
- **DigitalOutput_Q2**: Instanz des Typs logiBUS::io::DQ::logiBUS_QXA.
  - Parameter QI = TRUE
  - Parameter Output = Output_Q2

-----

## Zusammenfassung

Die Übung Uebung_267_AX demonstriert die modulare IEC 61499 Architektur in 4diac IDE.
