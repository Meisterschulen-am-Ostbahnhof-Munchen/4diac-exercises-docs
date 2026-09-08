# Uebung_010f_AX: AuxFunction2_X1 auf DigitalOutput_Q1 mit GreenWhiteBackground

![Uebung_010f_AX_network](./Uebung_010f_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung erweitert `Uebung_010b1_AX`: Zusätzlich zur Ansteuerung von `DigitalOutput_Q1` wird der Zustand von `AuxFunction2_X1` als Hintergrundfarbe (grün = EIN, weiß = AUS) zurückgemeldet. Sie zeigt, wie ein einzelnes Aux-Adaptersignal über `AX_SPLIT_2` auf zwei Verbraucher verteilt wird und warum für ein Auxiliary-Function-Type-2-Objekt der spezielle Baustein `GreenWhiteBackground2_AX` benötigt wird.

## Verwendete Funktionsbausteine (FBs)

- **AuxFunction2_X1**: Auxiliary-Eingang (Typ: `isobus::UT::io::Auxiliary::IN::Aux_IXA`)
    - **Parameter**: `QI = TRUE`, `u16ObjId = AuxFunction2_X1`
    - **Erklärung**: Liefert den booleschen Zustand des zugewiesenen physischen Aux-Tasters/Joysticks als AX-Adapterausgang `IN`.
- **AX_SPLIT_2**: Adapter-Signalverteiler (Typ: `adapter::events::unidirectional::AX_SPLIT_2`)
    - **Parameter**: keine
    - **Erklärung**: Vervielfältigt das eine AX-Signal von `AuxFunction2_X1` verlustfrei auf zwei Ziele, da ein Adapter-Plug nicht direkt an mehrere Sockets angeschlossen werden kann.
- **DigitalOutput_Q1**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Schaltet den physischen Ausgang `Q1` entsprechend dem Aux-Signal.

### Sub-Bausteine: GreenWhiteBackground2_AX

- **GreenWhiteBackground2_AX** (Typ: `MyLib::sys::GreenWhiteBackground2_AX`)
    - **Parameter**: `u16ObjIdA = AuxFunction2_X1`
    - **Erklärung**: Setzt die Hintergrundfarbe (grün/weiß) sowohl auf dem VT-Bildschirm als auch am Aux-Handle (Joystick) selbst. `GreenWhiteBackground2_AX` (nicht `GreenWhiteBackground1_AX`) ist hier zwingend nötig, weil `AuxFunction2_X1` ein Auxiliary-Function-Type-2-Objekt ist: seine Zuweisung wird an zwei getrennten Stellen angezeigt und muss daher intern sowohl `Q_BackgroundColour` (VT-Bildschirm) als auch `Q_BackgroundColourAux` (Aux-Handle) senden.

## Programmablauf und Verbindungen

1. `AuxFunction2_X1.IN` liefert bei jeder Zustandsänderung des Aux-Tasters ein AX-Adaptersignal.
2. Dieses Signal wird über `AX_SPLIT_2.IN` empfangen und auf `OUT1`/`OUT2` dupliziert.
3. `AX_SPLIT_2.OUT1` → `DigitalOutput_Q1.OUT`: der physische Ausgang `Q1` folgt direkt dem Aux-Zustand.
4. `AX_SPLIT_2.OUT2` → `GreenWhiteBackground2_AX.DI1`: dieselbe Information steuert parallel die Hintergrundfarbe auf VT-Bildschirm und Aux-Handle.

**Hinweis aus dem Netzwerk-Kommentar:** `AuxFunction2_X1` muss zweimal angegeben werden (einmal an `Aux_IXA.u16ObjId`, einmal an `GreenWhiteBackground2_AX.u16ObjIdA`) – dieser Nachteil wird in `Uebung_010f2_AX` durch Kapselung in eine eigene Subapp behoben.

## Zusammenfassung

Die Übung zeigt die Kombination aus Aux-Eingang, Adapter-Signalverteilung (`AX_SPLIT_2`) und der Auxiliary-Function-Type-2-spezifischen Hintergrundfarbrückmeldung (`GreenWhiteBackground2_AX`), die gleichzeitig VT-Bildschirm und Aux-Handle bedient. Sie bildet die Grundlage für die gekapselte Variante `Uebung_010f2_AX`.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
