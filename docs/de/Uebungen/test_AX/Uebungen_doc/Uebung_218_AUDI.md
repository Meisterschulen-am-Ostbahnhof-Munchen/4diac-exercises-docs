# Uebung_218_AUDI: Standard IEC 61131-3 AUDI_FB_CTD (Adapter Version, Rückwärtszähler, UDINT) mit Terminal-Ausgabe

![Uebung_218_AUDI_network](./Uebung_218_AUDI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung realisiert einen IEC 61131-3 konformen Rückwärtszähler (`AUDI_FB_CTD`) mit UDINT-Datentyp in der Adapter-Version. Der Zählerwert wird über einen Terminal-Baustein ausgegeben. Der Vorgabewert (Preset Value `PV`) wird durch einen separaten Baustein (`initval_AUDI`) auf 10 gesetzt. Die Übung demonstriert die Verwendung von Adapter-basierten Funktionsbausteinen und die Zusammenarbeit mit Ein-/Ausgangsmodulen sowie einer numerischen Terminalanzeige.

## Verwendete Funktionsbausteine (FBs)

- **Input_CD**: Digitaler Eingang für "Count Down"
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`
    - **Erklärung**: Liefert den Zustand von `I1` als AX-Adapter `IN` an `AUDI_FB_CTD.CD`.
- **Input_LD**: Digitaler Eingang für "Load"
    - **Typ**: `logiBUS::io::DI::logiBUS_IXA`
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`
    - **Erklärung**: Liefert den Zustand von `I2` als AX-Adapter `IN` an `AUDI_FB_CTD.LD`.
- **initval_AUDI**: Vorgabewert
    - **Typ**: `adapter::types::unidirectional::AUDI::initval::initval_AUDI`
    - **Parameter**: `INIT_VAL = 10`
    - **Erklärung**: Liefert beim Initialisieren den UDINT-Wert 10 als Adapter `OUT` an `AUDI_FB_CTD.PV`.
- **AUDI_FB_CTD**: Rückwärtszähler
    - **Typ**: `adapter::iec61131::counters::AUDI_FB_CTD`
    - **Parameter**: keine
    - **Erklärung**: Adapter-Version des IEC 61131-3 Abwärtszählers (CTD, UDINT). Eingänge als Adapter: `CD` (Count Down, AX), `LD` (Load, AX) und `PV` (Preset Value, AUDI). Ausgänge als Adapter: `Q` (AX, TRUE bei Zählerstand 0) und `CV` (aktueller Zählerstand, AUDI). Die Ausgänge werden nur bei Änderung gemeldet.
- **Output_Q1**: Digitaler Ausgang
    - **Typ**: `logiBUS::io::DQ::logiBUS_QXA`
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt `AUDI_FB_CTD.Q` als binäres Signal an `Q1` aus.
- **Q_NumericValue_AUDI**: Terminal-Ausgabe
    - **Typ**: `isobus::UT::Q::Q_NumericValue_AUDI`
    - **Parameter**: `u16ObjId = OutputNumber_N1`
    - **Erklärung**: Empfängt den Zählerstand (`CV`) über einen `AUDI`-Adapter (`u32NewValue`) und zeigt ihn im Objekt `OutputNumber_N1` an.

## Programmablauf und Verbindungen

Das Netzwerk enthält ausschließlich Adapterverbindungen (keine EventConnections):

1. `Input_CD.IN` → `AUDI_FB_CTD.CD`: `I1` zählt den Zähler herunter.
2. `Input_LD.IN` → `AUDI_FB_CTD.LD`: `I2` lädt den Zähler mit dem Preset-Wert.
3. `initval_AUDI.OUT` → `AUDI_FB_CTD.PV`: der Preset-Wert ist 10.
4. `AUDI_FB_CTD.Q` → `Output_Q1.OUT`: `Q1` leuchtet, sobald der Zählerstand 0 erreicht hat.
5. `AUDI_FB_CTD.CV` → `Q_NumericValue_AUDI.u32NewValue`: der aktuelle Zählerstand wird am Terminal angezeigt.

**Lernziele:**

- Verständnis des IEC 61131-3 Abwärtszählers (CTD) als Adapterbaustein.
- Umgang mit Adapterverbindungen in 4diac.
- Vorgabe von Zählerwerten über einen `initval`-Baustein.
- Visualisierung von Werten auf einem Terminal.

**Schwierigkeitsgrad:** Mittel

**Benötigte Vorkenntnisse:** Grundlagen der SPS-Programmierung, Umgang mit 4diac-IDE, Verständnis von Ereignis- und Datenflüssen.

**Durchführung:**

- Importieren Sie die Übung in die 4diac-IDE.
- Weisen Sie die Ein-/Ausgänge den entsprechenden realen oder simulierten Hardware-Pins zu.
- Starten Sie die Anwendung und beobachten Sie das Verhalten: Drücken von `I2` lädt den Zähler mit 10. Jedes Drücken von `I1` zählt um 1 herunter, bis 0 erreicht ist - dann leuchtet `Q1`. Der aktuelle Zählerstand wird im Terminal angezeigt.

## Zusammenfassung

Die Übung 218 demonstriert einen vollständigen IEC 61131-3 Abwärtszähler mit UDINT-Datentyp, der über Adapterverbindungen mit digitalen Ein- und Ausgängen sowie einer Terminalausgabe kommuniziert. Der Preset-Wert wird durch einen separaten `initval`-Baustein bereitgestellt.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
