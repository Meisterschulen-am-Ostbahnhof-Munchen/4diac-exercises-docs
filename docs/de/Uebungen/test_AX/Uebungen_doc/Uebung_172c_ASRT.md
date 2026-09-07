# Uebung_172c_ASRT: SET/RESET/TOGGLE-Latch mit logiBUS_IEA (AE-Adapter bereits eingebaut)

![Uebung_172c_ASRT_network](./Uebung_172c_ASRT_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung realisiert dieselbe Funktion wie `Uebung_172b_ASRT` (Taster `I1` = SET-Klick, `I2` = RESET-Klick, `I3` = TOGGLE-Klick, Verriegelung über `ASRT_AX_T_FF_SR`), spart sich aber die dort noch von Hand verdrahtete `AE_EVENT_TO_E`-Brücke pro Kanal: der Eingangsbaustein `logiBUS_IEA` liefert den `AE`-Adapter-Plug bereits eingebaut, genau wie `logiBUS_IX` → `logiBUS_IXA` für Pegelsignale, nur für Event-Eingänge.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**, **DigitalInput_CLK_I2**, **DigitalInput_CLK_I3**: logiBUS Klick-Eingänge mit eingebautem AE-Adapter (Typ: `logiBUS::io::DI::logiBUS_IEA`)
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`/`Input_I2`/`Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Kapseln intern einen `logiBUS_IE` und leiten dessen Klick-Event direkt auf einen eigenen `AE`-Adapter-Ausgang (`IN`) um – ohne separaten `AE_EVENT_TO_E`-Baustein wie in `Uebung_172b_ASRT`.
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

1. **Klick an I1 (SET)**: `DigitalInput_CLK_I1` (`logiBUS_IEA`) erkennt den Klick intern und stellt ihn direkt als `AE`-Adapter-Ausgang (`IN`) bereit.
2. **Klick an I2 (RESET)**: analog liefert `DigitalInput_CLK_I2.IN` den Reset-Zweig als `AE`-Adapter.
3. **Klick an I3 (TOGGLE)**: analog liefert `DigitalInput_CLK_I3.IN` den Toggle-Zweig als `AE`-Adapter.
4. **Bündeln zu ASRT**: die drei `IN`-Ausgänge gehen direkt auf `ASRT_3AE_TO_SRT_1.SET_IN`/`RESET_IN`/`TOGGLE_IN` – ohne Zwischenbaustein. Der Baustein fasst sie zu `ASRT_OUT` zusammen.
5. **Verriegeln**: `ASRT_3AE_TO_SRT_1.ASRT_OUT` wird auf `ASRT_AX_T_FF_SR_1.S_R_T` geführt. SET → `Q = TRUE`, RESET → `Q = FALSE`, TOGGLE → `Q` wechselt den Zustand.
6. **Ausgabe**: `ASRT_AX_T_FF_SR_1.Q` treibt direkt `DigitalOutput_Q1.OUT`.

## Zusammenfassung

Übung 172c zeigt exakt dasselbe Latch-Verhalten wie Übung 172 und 172b, kommt aber mit den wenigsten Bausteinen aus: `logiBUS_IEA` ersetzt `logiBUS_IE` und liefert den `AE`-Adapter-Plug bereits eingebaut, sodass die in 172b noch nötigen drei `AE_EVENT_TO_E`-Bausteine vollständig entfallen. Im Vergleich aller drei SubApps im 4diac-Editor zeigt sich derselbe funktionale Kern mit schrittweise kompakterer Verdrahtung.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
