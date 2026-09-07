# Uebung_204c_AX: Interlock ILOCK_CONFLICT_TRIP_PROTECT_AX (Trip bei Konflikt UND Schutzzeit nach Freigabe, via Adapter)

![Uebung_204c_AX_network](./Uebung_204c_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung erweitert `Uebung_204_AX` (die den einfachen `ILOCK_CONFLICT_TRIP_AX` ohne Schutzzeit verwendet) um eine zusätzliche Schutzzeit: Nach Freigabe des aktiven Eingangs wartet die Verriegelung `DT_PROTECT` (hier 1 Sekunde) ab, bevor eine neue Richtung übernommen (oder erneut ein Konflikt ausgelöst) werden kann. Es handelt sich um dieselbe Schutzzeit-Logik wie bei `ILOCK_SWITCH_PROTECT_AX` (`Uebung_205_AX`), hier kombiniert mit der Konflikterkennung (TRIP) aus `Uebung_204_AX`.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**, **DigitalInput_I2**: logiBUS Digitaleingänge (Typ: `logiBUS::io::DI::logiBUS_IXA`)
    - **Parameter**: QI = TRUE, Input = Input_I1 bzw. Input_I2
    - **Erklärung**: Liefern die beiden sich gegenseitig ausschließenden Anforderungssignale (z. B. Auf/Ab) als AX-Adaptersignale an die Verriegelung.
- **DigitalInput_Reset**: logiBUS Ereigniseingang (Typ: `logiBUS::io::DI::logiBUS_IE`)
    - **Parameter**: QI = TRUE, Input = Input_I3, InputEvent = BUTTON_SINGLE_CLICK
    - **Erklärung**: Löst bei einem Tasterklick das Rücksetzen (`EI_RESET`) der Verriegelung aus, nachdem ein Konflikt (TRIP) aufgetreten ist.
- **ILOCK_AX**: Interlock-Baustein (Typ: `logiBUS::signalprocessing::interlock::ILOCK_CONFLICT_TRIP_PROTECT_AX`)
    - **Parameter**: DT_PROTECT = T#1s
    - **Erklärung**: Verriegelt die beiden Eingänge `UP_IN`/`DOWN_IN` gegenseitig. Sind beide gleichzeitig aktiv, erkennt der Baustein einen Konflikt (TRIP) und setzt `TRIP_OUT`. Nach Freigabe des zuvor aktiven Eingangs wartet er zusätzlich `DT_PROTECT`, bevor eine neue Richtung angenommen wird (bzw. erneut ein TRIP ausgelöst wird, falls währenddessen der jeweils andere Eingang aktiviert wurde).
- **DigitalOutput_Q1**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: QI = TRUE, Output = Output_Q1
    - **Erklärung**: Gibt das freigegebene `UP_OUT`-Signal an die Peripherie weiter.
- **DigitalOutput_Q2**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: QI = TRUE, Output = Output_Q2
    - **Erklärung**: Gibt das freigegebene `DOWN_OUT`-Signal an die Peripherie weiter.
- **Trip_Anzeige**: logiBUS Digitalausgang (Typ: `logiBUS::io::DQ::logiBUS_QXA`)
    - **Parameter**: QI = TRUE, Output = Output_Q4
    - **Erklärung**: Zeigt den Konfliktzustand (`TRIP_OUT`) an, z. B. über eine Meldeleuchte.
- **E_TimeOut**: Zeitgeber-Ereignisquelle (Typ: `iec61499::events::E_TimeOut`)
    - **Parameter**: keine
    - **Erklärung**: Liefert dem Interlock-Baustein über die Adapterverbindung `timeOut` das periodische Zeitereignis, mit dem `DT_PROTECT` intern ausgewertet wird.

### Sub-Bausteine: keine

Die Übung verwendet keine weiteren Unterbausteine, alle FBs sind direkt auf der obersten Ebene der SubApp angeordnet.

## Programmablauf und Verbindungen

1. `DigitalInput_I1.IN` → `ILOCK_AX.UP_IN` und `DigitalInput_I2.IN` → `ILOCK_AX.DOWN_IN`: Die beiden Anforderungssignale gelangen über AdapterConnections in die Verriegelung.
2. `DigitalInput_Reset.IND` → `ILOCK_AX.EI_RESET`: Ein Klick auf den Reset-Taster setzt einen ausgelösten Konflikt zurück.
3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`: Der Interlock-Baustein bezieht darüber die Zeitbasis für die Schutzzeitüberwachung `DT_PROTECT`.
4. **Normalbetrieb**: Ist nur ein Eingang aktiv, wird `UP_OUT` bzw. `DOWN_OUT` freigegeben und über `Output_Q1`/`Output_Q2` ausgegeben.
5. **Konfliktfall (TRIP)**: Sind `UP_IN` und `DOWN_IN` gleichzeitig aktiv, setzt `ILOCK_AX.TRIP_OUT` → `Trip_Anzeige.OUT` und blockiert beide Ausgänge, bis über `DigitalInput_Reset` zurückgesetzt wird.
6. **Schutzzeit**: Wird der aktive Eingang losgelassen, akzeptiert `ILOCK_AX` erst nach Ablauf von `DT_PROTECT` (1 s) eine neue Richtung – wurde währenddessen der jeweils andere Eingang aktiviert, wird stattdessen erneut ein TRIP ausgelöst.

## Zusammenfassung

Die Übung 204c_AX kombiniert die Konflikterkennung aus `Uebung_204_AX` mit einer Schutzzeit nach Freigabe des aktiven Eingangs, wie sie `Uebung_205_AX` für einfache Richtungswechsel einführt. `ILOCK_CONFLICT_TRIP_PROTECT_AX` verhindert dadurch nicht nur gleichzeitige, widersprüchliche Anforderungen, sondern auch ein zu schnelles Umschalten der Richtung unmittelbar nach Freigabe – eine typische Anforderung an robuste Verriegelungslogik in der Automatisierungstechnik.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
