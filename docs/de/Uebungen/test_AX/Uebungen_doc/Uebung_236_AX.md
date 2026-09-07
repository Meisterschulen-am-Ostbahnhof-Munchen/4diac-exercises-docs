# Uebung_236_AX: AX_LAST_2 – derselbe Last-Wins-Effekt wie 229/230/233, ohne Edge-Detektor und Latch

![Uebung_236_AX_network](./Uebung_236_AX_network.svg)

* * * * * * * * * *

## Einleitung

Zwei Taster `I1`/`I2` steuern gemeinsam einen Ausgang `Q1` – dieselbe Grundidee wie in `Uebung_229_AX`/`Uebung_230_AX`/`Uebung_233_AX`, aber mit einem völlig anderen Baustein: `AX_LAST_2` ersetzt die komplette Kette aus Flankenerkennung, Zusammenführung und Latch durch einen einzigen Adapter-Baustein. **Wichtig:** `Uebung_236_AX` verhält sich beobachtbar **identisch** zu `Uebung_229_AX`/`Uebung_230_AX`/`Uebung_233_AX` – der Unterschied liegt im Bauplan, nicht im Verhalten.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**, **DigitalInput_I2**: logiBUS Digitaleingänge (Typ: `logiBUS::io::DI::logiBUS_IXA`)
    - **Parameter**: QI = TRUE, Input = Input_I1 bzw. Input_I2
    - **Erklärung**: Wandeln die physischen Tasterzustände in AX-Adaptersignale um. Der Adapter-Schreibevent (`IN.E1`) feuert dabei nur bei echter Zustandsänderung (über `logiBUS_IX.IND`) – er ist also selbst schon "flankengetriggert", genau wie `E_RF_TRIG` in `AX_RF_TRIG`/`AX_ASR_RF_TRIG`.
- **AX_LAST_2**: „Wer zuletzt schrieb, gewinnt“-Baustein (Typ: `adapter::events::unidirectional::AX_LAST_2`)
    - **Parameter**: keine
    - **Erklärung**: Besitzt zwei AX-Sockets (`IN1`, `IN2`) und einen AX-Plug (`OUT`). Sein ECC ist denkbar einfach: Welcher Socket zuletzt sein eigenes Schreibevent (`IN1.E1`/`IN2.E1`) ausgelöst hat, dessen aktueller Datenwert wird sofort 1:1 an `OUT` durchgereicht.
- **DigitalOutput_Q1**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: QI = TRUE, Output = Output_Q1
    - **Erklärung**: Gibt den aktuellen Zustand von `AX_LAST_2.OUT` an die Peripherie weiter.

### Sub-Bausteine: keine

Die Übung verwendet keine weiteren Unterbausteine, alle FBs sind direkt auf der obersten Ebene der SubApp angeordnet – `DigitalInput_I1`/`_I2` sind ohne jeden Zwischenbaustein direkt mit `AX_LAST_2.IN1`/`IN2` verbunden.

## Programmablauf und Verbindungen

1. `AX_LAST_2.IN1` ← `DigitalInput_I1.IN`: Der Zustand von Taster I1 wird direkt an den ersten Socket von `AX_LAST_2` angeschlossen.
2. `AX_LAST_2.IN2` ← `DigitalInput_I2.IN`: Der Zustand von Taster I2 wird direkt an den zweiten Socket angeschlossen – kein Flankendetektor, kein Merge-Baustein, kein Latch dazwischen.
3. `DigitalOutput_Q1.OUT` ← `AX_LAST_2.OUT`: `Q1` folgt jeweils dem Wert desjenigen Sockets, der zuletzt ein Schreibevent ausgelöst hat.
4. **Testablauf**: `I1` drücken → `Q1` EIN. `I2` zusätzlich drücken und halten → `Q1` bleibt EIN (I2 hat zuletzt geschrieben). `I2` loslassen (I1 bleibt gedrückt) → `Q1` fällt sofort auf AUS – identisch zum Verhalten von `Uebung_229_AX`/`Uebung_230_AX`, nur mit einem einzigen Baustein statt drei.

**Warum das identisch zu 229/230/233 ist, nicht anders:** `AX_RF_TRIG`/`AX_ASR_RF_TRIG` meldet die steigende Flanke (Drücken) als SET und die fallende Flanke (Loslassen) als RESET auf denselben Latch – jedes Loslassen irgendeines Tasters schaltet `Q1` sofort AUS, selbst wenn ein anderer Taster noch gedrückt gehalten wird. Das ist bereits dieselbe „letzte Flanke gewinnt“-Logik wie bei `AX_LAST_2`, kein Speicher, der sich merkt, dass ein anderer Taster noch aktiv ist. Der eigentliche Unterschied zwischen den beiden Bauplänen liegt in der Konstruktion, nicht im Verhalten:

- `Uebung_229_AX`: 2× `AX_RF_TRIG` + gemeinsamer `AX_SR` (lose EventConnections).
- `Uebung_230_AX`: 2× `AX_ASR_RF_TRIG` + `ASR_MERGE_2` + `ASR_AX_SR` (dieselbe Funktion, als Adapter-Bausteine).
- `Uebung_236_AX`: **ein einziger** Baustein (`AX_LAST_2`) ersetzt die komplette Kette aus Flankenerkennung, Zusammenführung und Latch, weil er direkt auf den rohen AX-Schreibereignissen arbeitet statt auf explizit erzeugten SET/RESET-Kommandos.

**Skalierbarkeit gegenüber `Uebung_233_AX` (`ASR_MERGE_3`):** `ASR_MERGE_N` existiert als ganze Familie (`ASR_MERGE_2..7`) – von 2 auf 3 Quellen zu gehen (230 → 233) heißt nur: einen weiteren `AX_ASR_RF_TRIG` anschließen und `ASR_MERGE_2` durch `ASR_MERGE_3` ersetzen, alles in einem einzigen Baustein. `AX_LAST_2` hat dagegen **keine** höherwertige Geschwister – es gibt kein `AX_LAST_3` in der Bibliothek. Eine dritte Rohquelle lässt sich nur durch Kaskadieren zweier `AX_LAST_2`-Instanzen einbinden (`OUT` der einen auf `IN1`/`IN2` der nächsten). Das bleibt aber semantisch korrektes Last-Wins über alle drei Quellen: `AX_LAST_2` kopiert den gewählten Wert nach `OUT.D1` **und** löst `OUT.E1` aus, jede Änderung an einer der ursprünglichen Quellen läuft also als frisches Ereignis durch die Kaskade – anders als bei einer reinen ODER-Verknüpfung geht dabei keine Aktualität verloren. Der Unterschied zu `ASR_MERGE_N` ist rein baulich: eine Familie mit einem Baustein pro Eingangszahl vs. N-1 kaskadierte Instanzen desselben Zwei-Eingänge-Bausteins – keine unterschiedliche Semantik.

## Zusammenfassung

Die Übung 236_AX zeigt, dass sich die aus 229/230/233 bekannte „Last-Wins“-Verriegelung von zwei Tastern auf einen gemeinsamen Ausgang mit einem einzigen Baustein (`AX_LAST_2`) nachbilden lässt, ohne einen expliziten Flankendetektor, Merge-Baustein oder Latch zu benötigen – weil `AX_LAST_2` direkt auf den ohnehin schon flankengetriggerten AX-Schreibereignissen arbeitet. Das beobachtbare Verhalten ist zu den drei Vorgängerübungen identisch; der Unterschied liegt ausschließlich in der Anzahl und Art der verwendeten Bausteine, sowie darin, dass `AX_LAST_2` – anders als die `ASR_MERGE_N`-Familie – nicht direkt auf mehr als zwei Quellen skaliert.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
