# Uebung_235_AX: Zweipunktregler mit Hysterese (VT-Variante)

![Uebung_235_AX_network](./Uebung_235_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung zeigt dasselbe Zweipunktregler-Muster wie `Uebung_234_AX` (Totzone + Hysterese um einen Mittelwert), simuliert den Messwert jedoch über ein VT-Eingabefeld statt über einen echten Analogsensor und meldet den Reglerzustand über die Hintergrundfarbe zweier VT-Textfelder zurück statt über physische Ausgänge.

## Verwendete Funktionsbausteine (FBs)

- **Messwert_N**: Terminal-Eingabe (Typ: `isobus::UT::io::NumericValue::NumericValue_PHYSA`)
    - **Parameter**: QI = TRUE, stObj = InputNumber_Messwert_N
    - **Erklärung**: Liest bei jeder Änderung von `InputNumber_Messwert` (VT-Objekt 9002, Bereich 0–1000) den physikalischen Wert als AR-Plug (`rPhys`) ein. Der Bediener simuliert damit im Pool `Workspace_Dreieck` den Analogsensor aus `Uebung_234_AX`.
- **HysteresisParams_AR**: Parameter-Baustein für die Hysterese-Schwellwerte
    - **Parameter**: rMI = 500.0, rDEAD = 20.0, rHYSTERESIS = 30.0
    - **Erklärung**: Stellt die drei Schwellwerte des Zweipunktreglers bereit – dieselben Werte wie in `Uebung_234_AX`.
- **DualHysteresis**: Zweipunktregler mit Totzone und Hysterese (Typ: `logiBUS::signalprocessing::hysteresis::DualHysteresis_AR_A2X`)
    - **Parameter**: QI = TRUE
    - **Erklärung**: Vergleicht den Messwert gegen `MI`: Steigt der Wert über `MI + DEAD + HYSTERESIS` (550), schaltet `UP`; fällt er unter `MI - DEAD - HYSTERESIS` (450), schaltet `DOWN`. Ausgeschaltet wird erst innerhalb der reinen Totzone `MI ± DEAD` (480–520) – die Differenz zwischen Ein- und Ausschaltpunkt verhindert Flattern.
- **A2X_2X_TO_2AX_1**: Entbündelung eines A2X-Signals in zwei AX-Signale (Typ: `adapter::conversion::unidirectional::A2X_2X_TO_2AX`)
    - **Parameter**: keine
    - **Erklärung**: `DualHysteresis.OUT` liefert UP/DOWN gebündelt als ein `A2X`-Signal. Dieser Baustein entbündelt es wieder in zwei einzelne `AX`-Signale, weil `GreenWhiteBackground1_AX` pro Instanz nur einen `DI1`-Eingang besitzt.

### Sub-Bausteine: GreenWhiteBackground1_AX_UP, GreenWhiteBackground1_AX_DOWN

- **GreenWhiteBackground1_AX_UP** (Typ: `MyLib::sys::GreenWhiteBackground1_AX`)
    - **Parameter**: u16ObjId = OutputString_UP
    - **Erklärung**: Färbt das VT-Textfeld `OutputString_UP` (VT-Objekt 11001) grün, solange `UP` aktiv ist, sonst weiß.
- **GreenWhiteBackground1_AX_DOWN** (Typ: `MyLib::sys::GreenWhiteBackground1_AX`)
    - **Parameter**: u16ObjId = OutputString_DOWN
    - **Erklärung**: Färbt das VT-Textfeld `OutputString_DOWN` (VT-Objekt 11002) grün, solange `DOWN` aktiv ist, sonst weiß.

## Programmablauf und Verbindungen

1. Ändert der Bediener `InputNumber_Messwert`, liefert `Messwert_N.rPhys` den neuen Wert.
2. `DualHysteresis.INPUT` ← `Messwert_N.rPhys`: Der simulierte Messwert gelangt an den Zweipunktregler.
3. `DualHysteresis.MI/DEAD/HYSTERESIS` ← `HysteresisParams_AR.MI/DEAD/HYSTERESIS`: Die Schwellwerte (500.0 / 20.0 / 30.0) werden fest vorgegeben.
4. `A2X_2X_TO_2AX_1.A2X_IN` ← `DualHysteresis.OUT`: Das kombinierte UP/DOWN-Signal wird entbündelt.
5. `GreenWhiteBackground1_AX_UP.DI1` ← `A2X_2X_TO_2AX_1.UP`: Der UP-Zustand färbt `OutputString_UP`.
6. `GreenWhiteBackground1_AX_DOWN.DI1` ← `A2X_2X_TO_2AX_1.DOWN`: Der DOWN-Zustand färbt `OutputString_DOWN`.
7. Am echten Terminal: Messwert deutlich über 550 → `OutputString_UP` grün, `OutputString_DOWN` bleibt weiß. Messwert deutlich unter 450 → umgekehrt. Messwert zwischen 480 und 520 → beide weiß. Werte zwischen 520–550 bzw. 450–480 (innerhalb der Hysterese, außerhalb der Totzone) zeigen das Hysterese-Verhalten: Der zuletzt aktive Zustand bleibt erhalten, bis die jeweilige Totzonen-Grenze erreicht wird.

## Zusammenfassung

Die Übung 235_AX ist das VT-Pendant zu `Uebung_234_AX`: Anstelle eines echten Analogsensors simuliert der Bediener den Messwert über ein VT-Eingabefeld, und anstelle physischer Ausgänge zeigt die Hintergrundfarbe zweier VT-Textfelder den UP-/DOWN-Zustand des Zweipunktreglers an. Die eigentliche Regellogik (`DualHysteresis_AR_A2X` mit Totzone und Hysterese um einen Mittelwert) ist in beiden Übungen identisch. Die dafür neu angelegten VT-Objekte – `InputNumber_Messwert` (9002) mit der zugehörigen Zahlenvariable `NumberVariable_Messwert` (21002) sowie `OutputString_UP` (11001) und `OutputString_DOWN` (11002) – liegen in `Workspace_Dreieck/DefaultPool/DefaultPool.jop`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
