# Uebung_026_AX: Spiegelabfolge (6)

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_026_AX (Spiegelabfolge (6)).

----

![Uebung_026_AX_network](./Uebung_026_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Spiegelabfolge (6)**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_026_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **SoftKey_UP_F1**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F1
  - Parameter InputEvent = SK_RELEASED
- **SoftKey_F2_DOWN**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F2
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F3_DOWN**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F3
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F9_DOWN**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F9
  - Parameter InputEvent = SK_PRESSED
- **SoftKey_F8_DOWN**: Instanz des Typs isobus::UT::io::Softkey::Softkey_IE.
  - Parameter QI = TRUE
  - Parameter u16ObjId = SoftKey_F8
  - Parameter InputEvent = SK_PRESSED
- **E_DELAY**: Instanz des Typs iec61499::events::E_DELAY.
  - Parameter DT = T#2s
- **E_REND_Ausfahren_Cyl_1**: Instanz des Typs iec61499::events::E_REND.
- **E_REND_Ausfahren_Cyl_2**: Instanz des Typs iec61499::events::E_REND.
- **E_REND_Einfahren_Cyl_2**: Instanz des Typs iec61499::events::E_REND.
- **E_REND_Einfahren_Cyl_1**: Instanz des Typs iec61499::events::E_REND.

### Verbindungen und Schnittstellen

**Ereignisverbindungen:**

- SoftKey_F2_DOWN.IND -> E_REND_Ausfahren_Cyl_1.EI2
- SoftKey_F3_DOWN.IND -> E_REND_Ausfahren_Cyl_2.EI2
- SoftKey_F9_DOWN.IND -> E_REND_Einfahren_Cyl_1.EI2
- SoftKey_F8_DOWN.IND -> E_REND_Einfahren_Cyl_2.EI2
- E_REND_Ausfahren_Cyl_2.EO -> E_DELAY.START
- SoftKey_UP_F1.IND -> E_REND_Ausfahren_Cyl_1.R
- SoftKey_F2_DOWN.IND -> E_REND_Ausfahren_Cyl_2.R
- E_DELAY.EO -> E_REND_Einfahren_Cyl_2.R
- SoftKey_F8_DOWN.IND -> E_REND_Einfahren_Cyl_1.R
- E_REND_Einfahren_Cyl_2.EO -> Q4.SET
- E_REND_Einfahren_Cyl_1.EO -> Q4.RESET
- Q4.EO1 -> E_REND_Einfahren_Cyl_1.EI1
- E_REND_Ausfahren_Cyl_1.EO -> Q2.SET
- E_REND_Ausfahren_Cyl_2.EO -> Q2.RESET
- Q2.EO1 -> E_REND_Ausfahren_Cyl_2.EI1
- E_DELAY.EO -> Q3.SET
- E_REND_Einfahren_Cyl_2.EO -> Q3.RESET
- Q3.EO1 -> E_REND_Einfahren_Cyl_2.EI1
- SoftKey_UP_F1.IND -> Q1.SET
- E_REND_Ausfahren_Cyl_1.EO -> Q1.RESET
- Q1.EO1 -> E_REND_Ausfahren_Cyl_1.EI1

### Hinweise aus dem Modell

> START-Knopf Ausfahren
> Endlage Ausfahren_Cyl_1
> Endlage Ausfahren_Cyl_2
> Ausfahren_Cyl_1
> Ausfahren_Cyl_2
> Einfahren Zeit gesteuert
> Endlage Einfahren_Cyl_1
> Einfahren_Cyl_2
> Endlage Einfahren_Cyl_2
> Einfahren_Cyl_1

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_026_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
