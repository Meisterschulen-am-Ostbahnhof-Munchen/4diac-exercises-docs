# Uebung_010f3_AX: SoftKey_F1 ODER AuxFunction2_X1 auf DigitalOutput_Q1 mit GreenWhiteBackground

![Uebung_010f3_AX_network](./Uebung_010f3_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung kombiniert `Uebung_010c_AX` (SoftKey mit Hintergrundfarbe) und `Uebung_010f_AX` (Aux mit Hintergrundfarbe) auf einen gemeinsamen Ausgang: dieselbe Funktion ist sowohl über den VT-SoftKey als auch über den physischen Aux-Taster/Joystick bedienbar. Sie zeigt den korrekten, idiomatischen Lösungsweg für diesen kombinierten Fall – als Kontrast dazu siehe die absichtliche Negativlösung `Uebung_010f4_AX`.

## Verwendete Funktionsbausteine (FBs)

- **SoftKey_F1**: VT-SoftKey-Eingang (Typ: `isobus::UT::io::Softkey::Softkey_IXA`)
    - **Parameter**: `QI = TRUE`, `u16ObjId = SoftKey_F1`
    - **Erklärung**: Liefert den booleschen Zustand des SoftKeys als AX-Adapterausgang `IN`.
- **AuxFunction2_X1**: Auxiliary-Eingang (Typ: `isobus::UT::io::Auxiliary::IN::Aux_IXA`)
    - **Parameter**: `QI = TRUE`, `u16ObjId = AuxFunction2_X1`
    - **Erklärung**: Liefert den booleschen Zustand des zugewiesenen physischen Aux-Tasters/Joysticks als AX-Adapterausgang `IN`.
- **AX_OR_2**: Adapter-ODER-Verknüpfung (Typ: `adapter::booleanOperators::AX_OR_2`)
    - **Parameter**: keine
    - **Erklärung**: Verknüpft die beiden AX-Signale von SoftKey und Aux-Taster per ODER zu einem gemeinsamen Signal für `DigitalOutput_Q1`.
- **AX_SPLIT_2**: Adapter-Signalverteiler (Typ: `adapter::events::unidirectional::AX_SPLIT_2`)
    - **Parameter**: keine
    - **Erklärung**: Verteilt das ODER-verknüpfte Signal auf zwei Ziele: den physischen Ausgang und die kombinierte Hintergrundfarbe.
- **DigitalOutput_Q1**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Schaltet den physischen Ausgang `Q1`, sobald SoftKey oder Aux-Taster aktiv sind.

### Sub-Bausteine: GreenWhiteBackground3_AX

- **GreenWhiteBackground3_AX** (Typ: `MyLib::sys::GreenWhiteBackground3_AX`)
    - **Parameter**: `u16ObjId = SoftKey_F1`, `u16ObjIdA = AuxFunction2_X1`
    - **Erklärung**: Nimmt genau einen normalen VT-Objekt-Parameter (`u16ObjId` = SoftKey) UND einen Aux-Objekt-Parameter (`u16ObjIdA` = Aux) gleichzeitig entgegen und setzt beide Hintergründe aus einem einzigen `DI1`-Eingang. Intern sendet er drei Kommandos (`Q_BackgroundColour` für den SoftKey, `Q_BackgroundColour` UND `Q_BackgroundColourAux` für die Aux-Funktion), nach außen genügt daher ein Split auf zwei statt drei Ziele.

## Programmablauf und Verbindungen

1. `SoftKey_F1.IN` → `AX_OR_2.IN1` und `AuxFunction2_X1.IN` → `AX_OR_2.IN2`: beide Bedienelemente liefern je ein AX-Signal an die ODER-Verknüpfung.
2. `AX_OR_2.OUT` → `AX_SPLIT_2.IN`: das kombinierte Signal wird auf zwei Verbraucher dupliziert.
3. `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT`: der physische Ausgang `Q1` schaltet EIN, sobald SoftKey ODER Aux-Taster aktiv ist.
4. `AX_SPLIT_2.OUT2` → `GreenWhiteBackground3_AX.DI1`: derselbe kombinierte Zustand steuert gleichzeitig beide Hintergrundfarben (SoftKey auf dem VT, Aux auf VT und Aux-Handle).

## Zusammenfassung

Die Übung zeigt, wie zwei unterschiedliche Bedienelement-Typen (SoftKey und Aux) über eine gemeinsame ODER-Verknüpfung denselben Ausgang steuern, und wie der dafür vorgesehene Baustein `GreenWhiteBackground3_AX` beide Rückmeldungen aus nur einem `DI1`-Eingang und mit nur einem `AX_SPLIT_2` bedient – die ressourcenschonende, korrekte Lösung im Vergleich zur bewusst fehlerhaften `Uebung_010f4_AX`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
