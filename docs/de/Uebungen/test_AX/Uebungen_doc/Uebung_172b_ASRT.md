# Uebung_172b_ASRT: SET/RESET/TOGGLE-Latch über AE-Adapter statt rohe Events

![Uebung_172b_ASRT_network](./Uebung_172b_ASRT_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung realisiert dieselbe Funktion wie `Uebung_172_ASRT` (Taster `I1` = SET-Klick, `I2` = RESET-Klick, `I3` = TOGGLE-Klick, Verriegelung über `ASRT_AX_T_FF_SR`), verdrahtet den Weg von den drei Klick-Events zum Latch jedoch über **AE-Adapter-Sockets** statt über rohe EventConnections. Wie bei `Uebung_171b_ASR` zeigt sich hier derselbe Grund: ein `AE`-Plug ist ein vollwertiger Adapter (weiterverarbeitbar z. B. mit `AE_SPLIT_2`), ein rohes Event dagegen nur über EventConnections verdrahtbar.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**, **DigitalInput_CLK_I3**: logiBUS Klick-Eingänge (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`/`Input_I2`/`Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Melden je einen einzelnen Tasterklick als Ereignis (`IND`) am jeweiligen physischen Eingang.
- **AE_EVENT_TO_E_SET**, **AE_EVENT_TO_E_RESET**, **AE_EVENT_TO_E_TOGGLE**: Ereignis-zu-Adapter-Brücke (Typ: `adapter::conversion::unidirectional::AE_EVENT_TO_E`)
    - **Parameter**: keine
    - **Erklärung**: Wandeln je ein rohes Klick-Event (`REQ`) in einen getypten `AE`-Adapter-Ausgang (`AE_OUT`) um.
- **ASRT_3AE_TO_SRT_1**: AE-zu-ASRT-Konverter (Typ: `adapter::conversion::unidirectional::ASRT_3AE_TO_SRT`)
    - **Parameter**: keine
    - **Erklärung**: Nimmt drei `AE`-Adapter-Eingänge (`SET_IN`, `RESET_IN`, `TOGGLE_IN`) entgegen und bündelt sie zu einem gemeinsamen `ASRT`-Adapter-Ausgang (`ASRT_OUT`).
- **ASRT_AX_T_FF_SR_1**: Set-Reset-Toggle-Latch (Typ: `adapter::events::unidirectional::ASRT_AX_T_FF_SR`)
    - **Parameter**: keine
    - **Erklärung**: Verriegelt den gebündelten `S_R_T`-Eingang: SET schaltet `Q` EIN, RESET schaltet `Q` AUS, TOGGLE kehrt den aktuellen Zustand um.
- **DigitalOutput_Q1**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt den Latch-Zustand `Q` als physisches Ausgangssignal aus.

### Sub-Bausteine: keine

Die Übung verwendet keine weiteren Unterbausteine, alle FBs sind direkt auf der obersten Ebene der SubApp angeordnet.

## Programmablauf und Verbindungen

1. **Klick an I1 (SET)**: `DigitalInput_CLK_I1.IND` löst `AE_EVENT_TO_E_SET.REQ` aus, der das Event in einen `AE`-Adapter-Ausgang wandelt.
2. **Klick an I2 (RESET)**: analog löst `DigitalInput_CLK_I2.IND` `AE_EVENT_TO_E_RESET.REQ` aus.
3. **Klick an I3 (TOGGLE)**: analog löst `DigitalInput_CLK_I3.IND` `AE_EVENT_TO_E_TOGGLE.REQ` aus.
4. **Bündeln zu ASRT**: die drei `AE_OUT`-Ausgänge gehen auf `ASRT_3AE_TO_SRT_1.SET_IN`/`RESET_IN`/`TOGGLE_IN`. Der Baustein fasst sie zu `ASRT_OUT` zusammen.
5. **Verriegeln**: `ASRT_3AE_TO_SRT_1.ASRT_OUT` wird auf `ASRT_AX_T_FF_SR_1.S_R_T` geführt. SET → `Q = TRUE`, RESET → `Q = FALSE`, TOGGLE → `Q` wechselt den Zustand.
6. **Ausgabe**: `ASRT_AX_T_FF_SR_1.Q` treibt direkt `DigitalOutput_Q1.OUT`.

## Zusammenfassung

Die Übung 172b demonstriert denselben SET/RESET/TOGGLE-Latch wie Übung 172, verdrahtet aber bewusst über AE-Adapter-Sockets statt rohe Events: je ein `AE_EVENT_TO_E` baut die Brücke vom Klick-Event zum getypten `AE`-Plug, `ASRT_3AE_TO_SRT` bündelt alle drei zu einem `ASRT`-Signal für das Latch `ASRT_AX_T_FF_SR`. Der Mehraufwand gegenüber der rohen Event-Variante lohnt sich, sobald das Signal selbst wie ein Adapter weiterverarbeitet werden soll.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
