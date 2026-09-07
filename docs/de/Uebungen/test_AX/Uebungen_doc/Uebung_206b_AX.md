# Uebung_206b_AX: Interlock ILOCK_T_FF_SR_AX (2 gegenseitig verriegelte Toggle-Flip-Flops, zusätzlich direktes Set/Reset auf FF1)

![Uebung_206b_AX_network](./Uebung_206b_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung erweitert `Uebung_206_AX` (zwei über `ILOCK_T_FF_AX` gegenseitig verriegelte Toggle-Flip-Flops) um den bislang letzten noch ungenutzten Interlock-Baustein: `ILOCK_T_FF_SR_AX`. Dieser bietet zusätzlich zum Toggle-Eingang (`CLK`) zwei direkte Ereigniseingänge `S` (Set) und `R` (Reset), die – nur an der ersten Instanz gezeigt – ebenfalls über die Verriegelungskette auf das jeweils andere Flip-Flop wirken.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: logiBUS Ereigniseingang (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: QI = TRUE, Input = Input_I1, InputEvent = BUTTON_SINGLE_CLICK
    - **Erklärung**: Klick auf I1 togglet FF1 (`ILOCK_T_FF_SR_1.CLK`).
- **DigitalInput_Set_I3**: logiBUS Ereigniseingang (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: QI = TRUE, Input = Input_I3, InputEvent = BUTTON_SINGLE_CLICK
    - **Erklärung**: Klick auf I3 setzt FF1 direkt (`ILOCK_T_FF_SR_1.S`), unabhängig vom vorherigen Zustand.
- **DigitalInput_Reset_I4**: logiBUS Ereigniseingang (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: QI = TRUE, Input = Input_I4, InputEvent = BUTTON_SINGLE_CLICK
    - **Erklärung**: Klick auf I4 setzt FF1 direkt zurück (`ILOCK_T_FF_SR_1.R`).
- **DigitalInput_CLK_I2**: logiBUS Ereigniseingang (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: QI = TRUE, Input = Input_I2, InputEvent = BUTTON_SINGLE_CLICK
    - **Erklärung**: Klick auf I2 togglet FF2 (`ILOCK_T_FF_SR_2.CLK`). Für FF2 sind keine direkten Set-/Reset-Eingänge verdrahtet.
- **ILOCK_T_FF_SR_1**, **ILOCK_T_FF_SR_2**: Verriegelbares Toggle-Flip-Flop mit Set/Reset (Typ: `logiBUS::signalprocessing::interlock::ILOCK_T_FF_SR_AX`)
    - **Parameter**: keine
    - **Erklärung**: Jede Instanz toggelt ihren Ausgang `Q` bei `CLK`, kann ihn aber auch direkt über `S`/`R` setzen bzw. zurücksetzen. Über die Adapterkette `ILOCK_OUT`→`ILOCK_IN` verriegeln sich beide Instanzen gegenseitig: Wird eine Instanz eingeschaltet (per CLK-Toggle ODER per direktem `S`), wird die andere automatisch ausgeschaltet.
- **DigitalOutput_Q1**, **DigitalOutput_Q2**: logiBUS Digitalausgänge (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: QI = TRUE, Output = Output_Q1 bzw. Output_Q2
    - **Erklärung**: Geben die Zustände von `ILOCK_T_FF_SR_1.Q` bzw. `ILOCK_T_FF_SR_2.Q` an die Peripherie weiter.

### Sub-Bausteine: keine

Die Übung verwendet keine weiteren Unterbausteine, alle FBs sind direkt auf der obersten Ebene der SubApp angeordnet.

## Programmablauf und Verbindungen

1. `DigitalInput_CLK_I1.IND` → `ILOCK_T_FF_SR_1.CLK`: Klick auf I1 togglet FF1.
2. `DigitalInput_Set_I3.IND` → `ILOCK_T_FF_SR_1.S`: Klick auf I3 setzt FF1 direkt (ohne Toggle-Logik).
3. `DigitalInput_Reset_I4.IND` → `ILOCK_T_FF_SR_1.R`: Klick auf I4 setzt FF1 direkt zurück.
4. `DigitalInput_CLK_I2.IND` → `ILOCK_T_FF_SR_2.CLK`: Klick auf I2 togglet FF2.
5. `ILOCK_T_FF_SR_1.ILOCK_OUT` → `ILOCK_T_FF_SR_2.ILOCK_IN`: Die Verriegelungskette überträgt jeden Zustandswechsel von FF1 an FF2, unabhängig davon, ob er durch CLK-Toggle oder durch direktes Set ausgelöst wurde.
6. `ILOCK_T_FF_SR_1.Q` → `DigitalOutput_Q1.OUT`, `ILOCK_T_FF_SR_2.Q` → `DigitalOutput_Q2.OUT`.
7. **Testablauf**: I1 (CLK FF1) klicken → Q1 EIN, Q2 automatisch AUS (wie in `Uebung_206_AX`). I3 (Set FF1) klicken → dasselbe Ergebnis, aber ohne Toggle-Verhalten – Q1 ist danach garantiert EIN, unabhängig vom vorherigen Zustand, und verriegelt FF2 genauso wie ein CLK-Toggle. I4 (Reset FF1) klicken → Q1 AUS, **Q2 bleibt dabei unverändert**, da ein Reset kein Verriegelungssignal an die Kette sendet – anders als Toggle-auf-EIN oder direktes Set.

## Zusammenfassung

Die Übung 206b_AX zeigt den letzten bislang ungenutzten Interlock-Baustein `ILOCK_T_FF_SR_AX`: Er erweitert das aus `Uebung_206_AX` bekannte gegenseitig verriegelte Toggle-Flip-Flop-Paar um direkte Set-/Reset-Eingänge. Wichtig ist die Asymmetrie im Verriegelungsverhalten: Ein Zustandswechsel auf EIN (per Toggle oder per direktem Set) wirkt stets verriegelnd auf das jeweils andere Flip-Flop, während ein direktes Reset die Verriegelungskette nicht beeinflusst und das Partner-Flip-Flop unverändert lässt.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
