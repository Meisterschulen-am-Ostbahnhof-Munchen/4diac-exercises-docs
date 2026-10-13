# Uebung_083_AX: Beispiel für AUDI_CTUD_UDINT (Adapter-basiert)

![Uebung_083_AX_network](./Uebung_083_AX_network.svg)

* * * * * * * * * *

## Einleitung

Dieser Artikel beschreibt die logiBUS®-Übung `Uebung_083_AX`. Sie ist die adapterbasierte Variante der Übung 083 und demonstriert einen Auf-/Abwärtszähler (`AUDI_CTUD_UDINT`) für vorzeichenlose 32-Bit-Ganzzahlen. Ziel ist, die adapterbasierte Steuerung eines Zählers und die Anzeige des Zählwerts auf einem ISOBUS-Terminal kennenzulernen.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: Taster "Zähler hoch"
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I1` → `AUDI_CTUD_UDINT.CU`.
- **DigitalInput_CLK_I2**: Taster "Zähler runter"
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I2` → `AUDI_CTUD_UDINT.CD`.
- **DigitalInput_CLK_I3**: Taster "Reset"
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Einzelklick auf `I3` → `AUDI_CTUD_UDINT.R`.
- **DigitalInput_CLK_I4**: Eingang `I4`
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Ist im Netzwerk instanziiert, aber mit keinem anderen Baustein verbunden (insbesondere nicht mit `LD`).
- **initval_AUDI**: Startwert für `PV`
    - **Typ**: `adapter::types::unidirectional::AUDI::initval::initval_AUDI`
    - **Parameter**: `INIT_VAL = 5`
    - **Erklärung**: Liefert beim Initialisieren den UDINT-Wert 5 als Adapter `OUT`, der an `AUDI_CTUD_UDINT.PV` geht.
- **AUDI_CTUD_UDINT**: Adapterbasierter Auf-/Abwärtszähler
    - **Typ**: `adapter::events::unidirectional::AUDI_CTUD_UDINT`
    - **Parameter**: keine
    - **Erklärung**: Ereignisse `CU` (hoch), `CD` (runter), `R` (Reset auf 0) und `LD` (CV mit PV laden; hier nicht verdrahtet). Adapterausgänge: `QU` (TRUE, wenn `CV >= PV`), `QD` (TRUE, wenn `CV <= 0`) und `CV` (Zählwert). `PV` ist ein Adaptereingang. Die Ausgänge werden nur bei Änderung gemeldet.
- **DigitalOutput_Q1**: Digitalausgang für `QU`
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Schaltet `Q1` gemäß `AUDI_CTUD_UDINT.QU`.
- **DigitalOutput_Q2**: Digitalausgang für `QD`
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q2`
    - **Erklärung**: Schaltet `Q2` gemäß `AUDI_CTUD_UDINT.QD`.
- **Q_NumericValue_AUDI**: Anzeige am Terminal
    - **Typ**: `isobus::UT::Q::Q_NumericValue_AUDI`
    - **Parameter**: `u16ObjId = OutputNumber_N1`
    - **Erklärung**: Zeigt den Zählwert über den `AUDI`-Adapter (`u32NewValue`) im VT-Zahlenfeld `OutputNumber_N1` an.

## Programmablauf und Verbindungen

1. `DigitalInput_CLK_I1.IND` → `AUDI_CTUD_UDINT.CU`: jeder Klick erhöht den Zähler um 1.
2. `DigitalInput_CLK_I2.IND` → `AUDI_CTUD_UDINT.CD`: jeder Klick verringert den Zähler um 1.
3. `DigitalInput_CLK_I3.IND` → `AUDI_CTUD_UDINT.R`: setzt den Zähler auf 0 zurück.
4. `initval_AUDI.OUT` → `AUDI_CTUD_UDINT.PV`: der Vorgabewert ist 5.
5. `AUDI_CTUD_UDINT.QU` → `DigitalOutput_Q1.OUT` und `AUDI_CTUD_UDINT.QD` → `DigitalOutput_Q2.OUT`: Statusanzeige des Zählers.
6. `AUDI_CTUD_UDINT.CV` → `Q_NumericValue_AUDI.u32NewValue`: der Zählwert wird am Terminal angezeigt.

**Verhalten**: `Q1` ist EIN, sobald der Zählwert den Vorgabewert 5 erreicht oder überschreitet; `Q2` ist EIN, solange der Zählwert 0 ist.

## Zusammenfassung

Die Übung zeigt einen vollständig adapterbasierten Zähler: Ereignisse von Tastern steuern `AUDI_CTUD_UDINT`, der Vorgabewert wird über einen `AUDI`-Adapter (`initval_AUDI`) geliefert, und Statusausgänge sowie Zählwert werden ohne zusätzliche Datenverdrahtung an Ausgänge und das Terminal übergeben.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
