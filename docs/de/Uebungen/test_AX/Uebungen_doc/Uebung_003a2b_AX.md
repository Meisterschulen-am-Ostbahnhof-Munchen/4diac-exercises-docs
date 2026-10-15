# Uebung_003a2b_AX: 2x R_IO mit IXA

![Uebung_003a2b_AX_network](./Uebung_003a2b_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung demonstriert die Steuerung von zwei digitalen Ausgängen (Q1 und Q2) über jeweils einen Rücksetz-Set-Funktionsbaustein (`AX_FB_R_IO`). Die Rücksetzeingänge beider Bausteine werden gemeinsam über den ersten Digitaleingang (I1) gesteuert, der als „Hausmeister-Aus“ (alles Aus mit einem Schalter) fungiert. Ein `AX_SPLIT_2` verteilt das Rücksetzsignal auf beide Kanäle. Gesetzt werden die Ausgänge über I2 (für Q1) und I3 (für Q2).

Ziel ist es, das Zusammenspiel von monostabilen Elementen (R_IO) mit Hardware-Ein- und Ausgängen sowie die Realisierung einer gemeinsamen Rücksetzfunktion zu verstehen.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_CLK_I1**: logiBUS Digitaleingang für das gemeinsame Reset („Hausmeister-Aus“)
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `PARAMS = ""` (beide in der Darstellung ausgeblendet), `Input = Input_I1`
    - **Erklärung**: Koppelt den physischen Eingang `I1` an die Adapterschnittstelle `IN`. Zusätzlich ist `INITO` mit `REQ` verbunden (EventConnection).
- **DigitalInput_CLK_I2**: logiBUS Digitaleingang zum Setzen von Q1
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `PARAMS = ""` (beide ausgeblendet), `Input = Input_I2`
    - **Erklärung**: Koppelt den physischen Eingang `I2` an die Adapterschnittstelle `IN`.
- **DigitalInput_CLK_I3**: logiBUS Digitaleingang zum Setzen von Q2
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `PARAMS = ""` (beide ausgeblendet), `Input = Input_I3`
    - **Erklärung**: Koppelt den physischen Eingang `I3` an die Adapterschnittstelle `IN`.
- **AX_SPLIT_2**: Signalverteiler
    - **Typ**: `adapter::events::unidirectional::AX_SPLIT_2`
    - **Parameter**: keine
    - **Erklärung**: Verteilt das Signal von `I1` unverändert auf die zwei Ausgänge `OUT1` und `OUT2`.
- **AX_FB_R_IO_Q1**: Rücksetz-Set-Element für Q1
    - **Typ**: `adapter::monostableElements::AX_FB_R_IO`
    - **Parameter**: keine
    - **Erklärung**: `IN` (von `I2`) setzt den Ausgang, `RESET1` (von `AX_SPLIT_2.OUT1`) setzt ihn zurück. Der Ausgang `OUT` führt zu `DigitalOutput_Q1`.
- **AX_FB_R_IO_Q2**: Rücksetz-Set-Element für Q2
    - **Typ**: `adapter::monostableElements::AX_FB_R_IO`
    - **Parameter**: keine
    - **Erklärung**: `IN` (von `I3`) setzt den Ausgang, `RESET1` (von `AX_SPLIT_2.OUT2`) setzt ihn zurück. Der Ausgang `OUT` führt zu `DigitalOutput_Q2`.
- **DigitalOutput_Q1**: logiBUS Digitalausgang für Q1
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt den Zustand von `AX_FB_R_IO_Q1.OUT` an den physischen Ausgang `Q1` weiter.
- **DigitalOutput_Q2**: logiBUS Digitalausgang für Q2
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q2`
    - **Erklärung**: Gibt den Zustand von `AX_FB_R_IO_Q2.OUT` an den physischen Ausgang `Q2` weiter.

## Programmablauf und Verbindungen

1. `DigitalInput_CLK_I1.INITO` → `DigitalInput_CLK_I1.REQ` (EventConnection): Der Baustein fordert nach der Initialisierung selbst den Eingangswert an.
2. `DigitalInput_CLK_I1.IN` → `AX_SPLIT_2.IN` (AdapterConnection): Das Signal von `I1` wird dem Verteiler zugeführt.
3. `AX_SPLIT_2.OUT1` → `AX_FB_R_IO_Q1.RESET1` und `AX_SPLIT_2.OUT2` → `AX_FB_R_IO_Q2.RESET1`: `I1` wirkt als gemeinsames Reset für beide Kanäle.
4. `DigitalInput_CLK_I2.IN` → `AX_FB_R_IO_Q1.IN`: `I2` setzt Q1.
5. `DigitalInput_CLK_I3.IN` → `AX_FB_R_IO_Q2.IN`: `I3` setzt Q2.
6. `AX_FB_R_IO_Q1.OUT` → `DigitalOutput_Q1.OUT` und `AX_FB_R_IO_Q2.OUT` → `DigitalOutput_Q2.OUT`: Die Zustände werden auf die physischen Ausgänge `Q1` bzw. `Q2` gelegt.

**Hinweise aus den Kommentaren im Netzwerk**:

- Hausmeister-Aus (alles Aus mit einem Schalter): Der Schalter müsste RASTEND sein. Ansonsten sind die Ausgänge nur so lange aus, wie der Taster gehalten wird.
- Vorteil dieser Lösung: Wird an `RESET1` nichts angeschlossen, ist der Baustein funktionsfähig. Das Prinzip entspricht dem `FB_RS`.
- Ein AND wäre auch möglich, dann wäre der Hausmeister ein Zustimmschalter.

## Zusammenfassung

Die Übung zeigt eine Schaltung zur Steuerung zweier Ausgänge mit einem gemeinsamen Rücksetzsignal: `I2` setzt `Q1`, `I3` setzt `Q2`, und `I1` setzt über `AX_SPLIT_2` beide `AX_FB_R_IO`-Bausteine zurück („Hausmeister-Aus“). Die Aufteilung des Rücksetzsignals über `AX_SPLIT_2` macht die Schaltung übersichtlich und erweiterbar.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
