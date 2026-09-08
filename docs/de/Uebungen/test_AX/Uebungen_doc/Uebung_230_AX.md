# Uebung_230_AX: Zwei Taster, ein SR-Latch (Last-Wins) mit ASR-Adaptern

![Uebung_230_AX_network](./Uebung_230_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung realisiert exakt dieselbe Funktion wie `Uebung_229_AX.md` (zwei Taster `I1`/`I2` schalten gemeinsam `Q1` über ein Last-Wins-SR-Latch), setzt sie jedoch komplett mit den neueren `ASR`-Adapter-Bausteinen um: statt jede Flanke als losen Event zu verkabeln, trägt ein `ASR`-Adapter Set und Reset gebündelt in einem Stecker. Die Übung demonstriert damit, wie sich dieselbe Logik typsicher über AdapterConnections statt über rohe EventConnections abbilden lässt.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**, **DigitalInput_I2**: logiBUS Digitaleingänge
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1` bzw. `Input_I2`
    - **Erklärung**: Koppeln die beiden physischen Taster-Eingänge an die Adapterschnittstelle.
- **AX_ASR_RF_TRIG_1**, **AX_ASR_RF_TRIG_2**: Flankenerkennung je Taster mit ASR-Ausgang
    - **Typ**: `adapter::events::unidirectional::AX_ASR_RF_TRIG`
    - **Parameter**: keine
    - **Erklärung**: Ersetzt `AX_RF_TRIG` aus `Uebung_229_AX.md`. Intern derselbe `E_RF_TRIG`-Kern (steigende/fallende Flanke), aber die beiden Events werden nicht als lose EventOutputs (`ER`/`EF`) exportiert, sondern direkt in einen gebündelten `ASR`-Adapter-Ausgang geschrieben (`Q.SET`/`Q.RESET`).
- **ASR_MERGE_2**: Zusammenführung der beiden ASR-Quellen
    - **Typ**: `adapter::events::unidirectional::ASR_MERGE_2`
    - **Parameter**: keine
    - **Erklärung**: Ersetzt die vier losen EventConnections aus `Uebung_229_AX.md`. Nimmt die beiden `ASR`-Adapter der beiden Taster entgegen und führt SET mit SET, RESET mit RESET zusammen — dieselbe ODER-Verknüpfung wie zuvor, jetzt aber als eigener, wiederverwendbarer, typsicherer Baustein statt Ad-hoc-Verkabelung.
- **ASR_AX_SR**: gemeinsame Set-Reset-Verriegelung mit gebündeltem ASR-Eingang
    - **Typ**: `adapter::events::unidirectional::ASR_AX_SR`
    - **Parameter**: keine
    - **Erklärung**: Ersetzt `AX_SR` aus `Uebung_229_AX.md`. Identischer ECC (Set-dominant, START→SET→RESET→SET), aber Set und Reset kommen gebündelt über einen einzigen `ASR`-Socket (`S_R`) statt als zwei lose EventInputs.
- **DigitalOutput_Q1**: logiBUS Digitalausgang
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt den Zustand von `ASR_AX_SR.Q` an den physischen Ausgang `Q1` weiter.

### Sub-Bausteine: keine

Die Übung verwendet keine weiteren Unterbausteine, alle FBs sind direkt auf der obersten Ebene der SubApp angeordnet.

## Programmablauf und Verbindungen

Diese Version verwendet ausschließlich AdapterConnections — es existiert kein einziges EventConnections-Element mehr:

1. `DigitalInput_I1.IN` → `AX_ASR_RF_TRIG_1.QI` und `DigitalInput_I2.IN` → `AX_ASR_RF_TRIG_2.QI`: die physischen Tasterzustände werden an die jeweilige Flankenerkennung übergeben.
2. `AX_ASR_RF_TRIG_1.Q` → `ASR_MERGE_2.IN1` und `AX_ASR_RF_TRIG_2.Q` → `ASR_MERGE_2.IN2`: die gebündelten Set/Reset-Signale beider Taster laufen in den Merge-Baustein.
3. `ASR_MERGE_2.OUT` → `ASR_AX_SR.S_R`: das zusammengeführte ASR-Signal steuert die gemeinsame Verriegelung.
4. `ASR_AX_SR.Q` → `DigitalOutput_Q1.OUT`: der Latch-Zustand wird auf den physischen Ausgang `Q1` gelegt.

**Verhalten identisch zu `Uebung_229_AX.md`**: Es handelt sich weiterhin um eine "Last-Wins"-Verriegelung, keine Halte-Logik. Drückt man `I1`, geht `Q1` EIN; lässt man `I1` los, geht `Q1` sofort wieder AUS, selbst wenn `I2` noch gedrückt gehalten wird — und umgekehrt. `ASR_MERGE_2` bildet dieselbe ODER-Verknüpfung von SET mit SET und RESET mit RESET ab, die in `Uebung_229_AX.md` durch die parallel geschalteten EventConnections entstand. Das Verhalten ist von außen ununterscheidbar von `Uebung_229_AX.md`; es unterscheidet sich ausschließlich der Bauplan dahinter — ein Vergleich der beiden SubApps im 4diac-Editor zeigt, dass `Uebung_229_AX.md` ein `EventConnections`-Element mit vier Verbindungen besitzt, während `Uebung_230_AX.md` gar keines hat.

## Zusammenfassung

Die Übung 230 zeigt, wie sich eine mit losen Event-Verbindungen aufgebaute Logik (`Uebung_229_AX.md`) vollständig auf typsichere Adapter-Bausteine umbauen lässt, ohne das Verhalten zu verändern. `AX_ASR_RF_TRIG`, `ASR_MERGE_2` und `ASR_AX_SR` kapseln dieselbe Flankenerkennung, ODER-Verknüpfung und Set-Reset-Logik wie zuvor, jetzt aber als wiederverwendbare, über AdapterConnections verdrahtete Bausteine. Die Übung verdeutlicht damit den Mehrwert von Adaptertechnologie gegenüber Ad-hoc-Event-Verkabelung bei gleichbleibender Funktion.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
