# Uebung_006g_AX: SR-Flip-Flop via INIT_ON_OFF, ausgelöst von zwei E_TRIG

![Uebung_006g_AX_network](./Uebung_006g_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung setzt ein SR-Flip-Flop (`AX_SR`) nicht über direkte Event-Leitungen, sondern über das Broadcast-Verhalten von `E_TRIG` an: Zwei `E_TRIG`-Bausteine lösen die unverbundenen Ereignis-Eingänge `INIT_ON` bzw. `INIT_OFF` des Bausteins `INIT_ON_OFF` aus. Dessen durchgereichte Ausgänge `INITO_ON` und `INITO_OFF` setzen und rücksetzen das Flip-Flop, dessen Zustand den Ausgang `Q1` steuert. Ein Klick auf `I1` schaltet `Q1` ein, ein Klick auf `I2` schaltet `Q1` aus.

## Verwendete Funktionsbausteine (FBs)

- **START**: logiBUS Eingang mit Klick-Erkennung für den Start-Taster
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Erkennt einen einzelnen Klick auf `I1` und meldet ihn als Event `IND`.
- **STOP**: logiBUS Eingang mit Klick-Erkennung für den Stopp-Taster
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Erkennt einen einzelnen Klick auf `I2` und meldet ihn als Event `IND`.
- **E_TRIG_ON**: Auslöser für den Ereignistyp `EInitOn`
    - **Typ**: [`iec61499::events::E_TRIG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_TRIG/)
    - **Parameter**: `EVENTTYPE = 'EInitOn'`
    - **Erklärung**: Löst bei `REQ` alle unverbundenen Ereignis-Eingänge mit dem Namen `EInitOn` im Netzwerk aus.
- **E_TRIG_OFF**: Auslöser für den Ereignistyp `EInitOff`
    - **Typ**: [`iec61499::events::E_TRIG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_TRIG/)
    - **Parameter**: `EVENTTYPE = 'EInitOff'`
    - **Erklärung**: Löst bei `REQ` alle unverbundenen Ereignis-Eingänge mit dem Namen `EInitOff` im Netzwerk aus.
- **INIT_ON_OFF**: Durchreicher für die beiden Init-Kanäle
    - **Typ**: `iec61131::booleanOperators::INIT_ON_OFF`
    - **Parameter**: keine
    - **Erklärung**: Besitzt neben `INIT` und `REQ` zwei eigenständige Pass-Through-Kanäle: `INIT_ON` → `INITO_ON` (Ereignistyp `EInitOn`) und `INIT_OFF` → `INITO_OFF` (Ereignistyp `EInitOff`). Die Eingänge `INIT_ON` und `INIT_OFF` bleiben in dieser Übung bewusst unverbunden (siehe Hinweis im Modell).
- **AX_SR**: Set-Reset-Flip-Flop mit Adapter-Ausgang
    - **Typ**: [`adapter::events::unidirectional::AX_SR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SR/)
    - **Parameter**: keine
    - **Erklärung**: Wird über die Events `S` (Set) und `R` (Reset) gesteuert und stellt den Zustand am Adapter `Q` bereit.
- **DigitalOutput_Q1**: logiBUS Digitalausgang
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt den Zustand von `AX_SR.Q` an den physischen Ausgang `Q1` weiter.

## Programmablauf und Verbindungen

1. `START.IND` → `E_TRIG_ON.REQ` (EventConnection): ein Klick auf `I1` löst den Trigger für `EInitOn` aus.
2. `STOP.IND` → `E_TRIG_OFF.REQ` (EventConnection): ein Klick auf `I2` löst den Trigger für `EInitOff` aus.
3. `INIT_ON_OFF.INITO_ON` → `AX_SR.S` und `INIT_ON_OFF.INITO_OFF` → `AX_SR.R` (EventConnections): die durchgereichten Ereignisse setzen bzw. rücksetzen das Flip-Flop.
4. `AX_SR.Q` → `DigitalOutput_Q1.OUT` (AdapterConnection): der Flip-Flop-Zustand wird auf den physischen Ausgang `Q1` gelegt.

**Hinweis zum Broadcast**: `INIT_ON` und `INIT_OFF` von `INIT_ON_OFF` sind nicht verbunden. `E_TRIG` sendet seinen `EVENTTYPE` an alle unverbundenen Ereignis-Eingänge dieses Typs im Netzwerk – daher reagiert hier nur der jeweils passende Kanal (`EInitOn` bzw. `EInitOff`).

**Verhalten**: Klick auf `I1` → `E_TRIG_ON` → `INIT_ON_OFF.INITO_ON` → `AX_SR.S`: `Q1` wird eingeschaltet. Klick auf `I2` → `E_TRIG_OFF` → `INIT_ON_OFF.INITO_OFF` → `AX_SR.R`: `Q1` wird ausgeschaltet.

## Zusammenfassung

Die Übung `Uebung_006g_AX` zeigt, dass ein SR-Flip-Flop auch ohne direkte Event-Leitungen zwischen Taster und Flip-Flop geschaltet werden kann: `E_TRIG` broadcastet einen Ereignistyp an unverbundene Ereignis-Eingänge, `INIT_ON_OFF` reicht die beiden Kanäle `EInitOn` und `EInitOff` getrennt durch und `AX_SR` setzt bzw. rücksetzt daraus den Ausgang `Q1`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
