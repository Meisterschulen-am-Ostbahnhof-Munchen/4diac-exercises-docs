# Uebung_039: Spiegelabfolge V2 mit Schrittkette

[![NotebookLM](media/NotebookLM_logo.png)](https://notebooklm.google.com/notebook/a6872e59-1dfc-4132-a118-aff1bc7bc944)

![Uebung_039_network](./Uebung_039_network.svg)

* * * * * * * * * *

## Einleitung

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_039`. Diese Übung ist speziell auf die Ansteuerung von hydraulischen oder pneumatischen Wegeventilen zugeschnitten.

Ziel ist die Realisierung einer komplexen Spiegelabfolge. Im Gegensatz zu einfachen Zylindern müssen bei Wegeventilen oft Zustände gehalten werden (Mittelstellung gesperrt), was eine präzise zeitliche und ereignisbasierte Steuerung der Spulen erfordert.

Die Subapplikation `Uebung_039.SUB` nutzt einen 5-Schritt-Sequenzer (`sequence_ET_05`). Die Ansteuerung der Hardware erfolgt über vier Instanzen der wiederverwendbaren SubApp `MyLib::sys::SoftkeyOverride_TO_QX_BG`, die den jeweiligen Ventilzustand auch visuell auf dem ISOBUS-Terminal durch Farbumschlag des zugehörigen Softkeys rückmelden.

## Verwendete Funktionsbausteine (FBs)

- **SoftKey_UP_F1**: VT-Softkey als Start-Taster
    - **Typ**: `isobus::UT::io::Softkey::Softkey_IE`
    - **Parameter**: `QI = TRUE`, `u16ObjId = SoftKey_F1`, `InputEvent = SK_RELEASED`
    - **Erklärung**: Meldet das Loslassen von `SoftKey_F1` als Event `IND`.
- **DigitalInput_DOWN_I1** bis **DigitalInput_DOWN_I4**: logiBUS Eingänge mit Erkennung des Tastendrucks
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1` / `Input_I2` / `Input_I3` / `Input_I4`, `InputEvent = BUTTON_PRESS_DOWN`
    - **Erklärung**: Jeder Eingang meldet das Herunterdrücken seines Tasters als Event `IND`.
- **sequence_05**: 5-Schritt-Sequenzer (Weiterschaltung per Ereignis oder Zeit)
    - **Typ**: `logiBUS::utils::sequence::combi::sequence_ET_05`
    - **Parameter**: `DT_S1_S2 = NO_TIME`, `DT_S2_S3 = NO_TIME`, `DT_S3_S4 = T#5s`, `DT_S4_S5 = NO_TIME`, `DT_S5_START = NO_TIME`
    - **Erklärung**: Schrittkette mit den Zuständen `START` (0) und `State_01` bis `State_05`. Jeder Übergang erfolgt über das zugehörige Ereignis (`START_S1`, `S1_S2`, ...) oder - sofern eine Zeit ungleich `NO_TIME` gesetzt ist - automatisch nach Ablauf der Zeit. Ausgänge: `STATE_NR` (mit `CNF`) sowie je Schritt `DO_Sx` mit `EO_Sx`. Nur `DT_S3_S4` ist mit 5 s belegt, alle anderen Übergänge warten auf ihr Ereignis.
- **E_TimeOut**: Zeitgeber für den Sequenzer
    - **Typ**: `iec61499::events::E_TimeOut`
    - **Parameter**: keine
    - **Erklärung**: Stellt über den Adapter `TimeOutSocket` die Timeout-Funktion für `sequence_05.timeOut` bereit.
- **NumbAnzeig**: Anzeige der Schrittnummer (SubApp-Instanz)
    - **Typ**: `MyLib::sys::SINT_TO_Q_NumericValue`
    - **Parameter**: `u16ObjId = OutputNumber_N1`
    - **Erklärung**: Wandelt den SINT-Wert `NewValue` (hier `STATE_NR`) in UINT und schreibt ihn per `Q_NumericValue` in das VT-Zahlenfeld `OutputNumber_N1`.
- **Q1**, **Q2**, **Q3**, **Q4**: Ausgang mit Handbedienung und Rückmeldung (SubApp-Instanzen)
    - **Typ**: `MyLib::sys::SoftkeyOverride_TO_QX_BG`
    - **Parameter**: `Q1`: `u16ObjId = SoftKey_F8`, `Output = Output_Q1`; `Q2`: `u16ObjId = SoftKey_F2`, `Output = Output_Q2`; `Q3`: `u16ObjId = SoftKey_F9`, `Output = Output_Q3`; `Q4`: `u16ObjId = SoftKey_F3`, `Output = Output_Q4`
    - **Erklärung**: Das Programmsignal `OUT` wird intern per `OR_2` mit dem gedrückten Softkey (`Softkey_IX`) ODER-verknüpft und auf den Digitalausgang (`logiBUS_QX`) gelegt; derselbe Zustand färbt über `GreenWhiteBackground1` den Hintergrund des Softkeys.

## Programmablauf und Verbindungen

1. `SoftKey_UP_F1.IND` → `sequence_05.START_S1`: Loslassen von `SoftKey_F1` startet die Kette (Schritt 1).
2. `DigitalInput_DOWN_I1.IND` → `sequence_05.S1_S2`: `I1` schaltet von Schritt 1 nach Schritt 2.
3. `DigitalInput_DOWN_I2.IND` → `sequence_05.S2_S3`: `I2` schaltet von Schritt 2 nach Schritt 3.
4. Schritt 3 → Schritt 4: kein Taster, sondern automatisch nach `DT_S3_S4 = T#5s` (`sequence_05.timeOut` ↔ `E_TimeOut.TimeOutSocket`). Der Eingang `S3_S4` ist nicht verdrahtet.
5. `DigitalInput_DOWN_I3.IND` → `sequence_05.S4_S5`: `I3` schaltet von Schritt 4 nach Schritt 5.
6. `DigitalInput_DOWN_I4.IND` → `sequence_05.S5_START`: `I4` beendet die Kette (zurück zu `START`).
7. Ausgänge: `EO_S1`/`DO_S1` → `Q1.REQ`/`Q1.OUT`, `EO_S2`/`DO_S2` → `Q2`, `EO_S4`/`DO_S4` → `Q3`, `EO_S5`/`DO_S5` → `Q4`. Schritt 3 hat keinen Ausgang.
8. `sequence_05.STATE_NR` → `NumbAnzeig.NewValue` und `sequence_05.CNF` → `NumbAnzeig.CNF`: die aktuelle Schrittnummer wird im VT angezeigt.

**Verhalten**: Die Kette wird manuell durch die physischen Taster (`I1` bis `I4`) weitergeschaltet, wobei der zentrale Zeitschritt (5 s bei `DT_S3_S4`) eine automatische Sicherheits- oder Wartephase einfügt. Pro Schritt ist nur der jeweilige Ausgang aktiv (`Q1` in Schritt 1, `Q2` in Schritt 2, in Schritt 3 keiner, `Q3` in Schritt 4, `Q4` in Schritt 5).

## Zusammenfassung

Die Übung zeigt die Kombination aus freier Bedienbarkeit (ereignisgesteuerte Übergänge per Taster) und erzwungenen Prozesszeiten (zeitgesteuerter Übergang von Schritt 3 nach 4) in einer Schrittkette für Wegeventile. Die Ausgänge werden über die generische SubApp `SoftkeyOverride_TO_QX_BG` angesteuert, sodass jede Spule zusätzlich per Softkey von Hand betätigt werden kann und der Zustand auf dem Terminal sichtbar ist.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
