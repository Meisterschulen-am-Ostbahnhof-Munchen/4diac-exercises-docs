# Uebung_271_AX: Dialog: MessageBox OK, modal: Test-Buttons gesperrt, RESULT im Zahlenfeld

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_271_AX (Dialog: MessageBox OK, modal: Test-Buttons gesperrt, RESULT im Zahlenfeld).

----

![Uebung_271_AX_network](./Uebung_271_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Dialog: MessageBox OK, modal: Test-Buttons gesperrt, RESULT im Zahlenfeld**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_271_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **SoftKey_Show**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_RELEASED
- **MsgBox**: Instanz des Typs isobus::UT::dialog::DLG_MessageBox.
  - Parameter TEXT_ID = OutputString_Text_OK
  - Parameter ICON = DLG_ICON_WARNING
  - Parameter BUTTONS = DLG_BTN_OK
  - Parameter MODAL = TRUE
- **RESULT_TO_UDINT**: Instanz des Typs iec61131::conversion::F_USINT_TO_UDINT.
- **Q_Result**: Instanz des Typs isobus::UT::Q::Q_NumericValue.
  - Parameter QI = TRUE
  - Parameter u16ObjId = OutputNumber_Result
- **Button_Test1**: Instanz des Typs isobus::UT::io::Button::Button_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = Button_Test1
  - Parameter InputEvent = BT_RELEASED_UNLATCHED
- **Q_Test1**: Instanz des Typs isobus::UT::Q::Q_NumericValue.
  - Parameter QI = TRUE
  - Parameter u16ObjId = OutputNumber_Result
  - Parameter u32NewValue = UDINT#101
- **Button_Test2**: Instanz des Typs isobus::UT::io::Button::Button_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = Button_Test2
  - Parameter InputEvent = BT_RELEASED_UNLATCHED
- **Q_Test2**: Instanz des Typs isobus::UT::Q::Q_NumericValue.
  - Parameter QI = TRUE
  - Parameter u16ObjId = OutputNumber_Result
  - Parameter u32NewValue = UDINT#102

-----

## Zusammenfassung

Die Übung Uebung_271_AX demonstriert die modulare IEC 61499 Architektur in 4diac IDE.
