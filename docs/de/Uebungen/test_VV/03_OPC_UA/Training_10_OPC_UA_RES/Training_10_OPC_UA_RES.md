# Training_10_OPC_UA_RES: Hoch/Runter-Schalter über OPC-UA — zwei Taster mit Interlock und Schutzzeit

![Training_10_OPC_UA_RES_network](./Training_10_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_10_OPC_UA_RES` ist eine verteilte Hoch/Runter-Schaltung: Gerät A liest die beiden physischen Taster `Input_I1` (Hoch) und `Input_I2` (Runter) und schreibt beide aktiv per OPC-UA (`CLIENT_1_0`) auf die von Gerät B überwachten Knoten. Gerät B führt beide Zustände in [`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/) (Last-Wins mit Schutzzeit) zusammen und schaltet `Output_Q7` und `Output_Q8`. Beide Taster sind tastend (spring-return); das Loslassen meldet sich als eigenes FALSE-Event über `logiBUS_IXA`.

Das Protokoll steckt im Composite (`SUB style`), nicht in der Resource des Geräts. Dies ist die erste Übung der Dreierserie Training_10–12 (Training_11 ersetzt die Taster durch VT-SoftKeys, Training_12 bündelt die Transportschicht).

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_10_network.svg) | A (`192.168.1.11`) | Zwei Instanzen von [`logiBUS_IXA_TO_CLIENT_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_CLIENT_OPC/) (`UpTaster`: `Input` = `Input_I1`, `ID` = `SW1_UP_REMOTE_WRITE`; `DownTaster`: `Input` = `Input_I2`, `ID` = `SW1_DOWN_REMOTE_WRITE`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_10_network.svg) | B (`192.168.1.12`) | 2x [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/), [`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/), [`E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/), 2x [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten (Schreiben auf anderer Station / lokal lesen) | Knoten | Bedeutung |
|---|---|---|
| `SW1_UP_REMOTE_WRITE / SW1_UP_LOCAL_READ` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Knoten auf B; A schreibt (`WRITE`), B liest lokal (`READ`) |
| `SW1_DOWN_REMOTE_WRITE / SW1_DOWN_LOCAL_READ` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Knoten auf B; A schreibt (`WRITE`), B liest lokal (`READ`) |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A): `UpTaster` liest `Input_I1`, `DownTaster` liest `Input_I2`; beide ([`logiBUS_IXA_TO_CLIENT_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/logiBUS_IXA_TO_CLIENT_OPC/)) schreiben den Zustand per OPC-UA-`WRITE` auf den Knoten von Gerät B (`SW1_UP_REMOTE_WRITE` bzw. `SW1_DOWN_REMOTE_WRITE`). Es gibt keine Verbindungen innerhalb der SubApp.
2. **`SubApp_PC_B`** (Gerät B), Adapterverbindungen:

    1. `SUBSCRIBE_UP.OUT` → `ILOCK_AX.UP_IN`
    2. `SUBSCRIBE_DOWN.OUT` → `ILOCK_AX.DOWN_IN`
    3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`
    4. `ILOCK_AX.UP_OUT` → `DigitalOutput_Q7.OUT`
    5. `ILOCK_AX.DOWN_OUT` → `DigitalOutput_Q8.OUT`

**Parameter**: `SUBSCRIBE_UP`/`SUBSCRIBE_DOWN` ([`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/)): `QI` = `TRUE`, `ID` = `SW1_UP_LOCAL_READ` bzw. `SW1_DOWN_LOCAL_READ`; `ILOCK_AX` ([`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/)): `DT_PROTECT` = `T#1s`; `DigitalOutput_Q7`/`DigitalOutput_Q8` ([`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/)): `QI` = `TRUE`, `Output` = `Output_Q7` bzw. `Output_Q8`.

## Technische Besonderheiten

- **Kein Zwischenbaustein nötig**: `AX_SUBSCRIBE_1.OUT` ist bereits vom Adaptertyp `AX` — demselben Typ, den `ILOCK_SWITCH_PROTECT_AX.UP_IN`/`.DOWN_IN` als Socket erwarten.
- **Last-Wins mit Schutzzeit**: `ILOCK_SWITCH_PROTECT_AX` arbitriert Hoch und Runter; `DT_PROTECT` = `T#1s` legt die Schutzzeit fest, den Timer stellt `E_TimeOut` über den `timeOut`-Socket.
- **FB-Netz direkt im Composite**: Die Kombination (2x Subscribe + Interlock + Schutzzeit-Timer + 2x Ausgang) ist übungsspezifisch und steckt deshalb direkt in `SubApp_PC_B`.
- **Tastende Taster**: Das Loslassen eines Tasters wird als eigenes FALSE-Event über `logiBUS_IXA` gemeldet.

## Lernziele

- Verteilte Hoch/Runter-Schaltung: Gerät A schreibt aktiv (`CLIENT_1_0`), Gerät B überwacht lokal (`AX_SUBSCRIBE_1`).
- Zwei Adapter-Sockets (`AX`) direkt in `ILOCK_SWITCH_PROTECT_AX` führen (Last-Wins, Schutzzeit `DT_PROTECT`).
- Das Muster „SUB style“: Protokoll im Composite statt in der Resource.

**Schwierigkeitsgrad**: Fortgeschritten  
**Vorkenntnisse**: `Training_09_OPC_UA_RES`.

## Zusammenfassung

`Training_10_OPC_UA_RES` zeigt die Grundform der Serie: zwei physische, tastende Taster auf Gerät A, einzelne Netzwerk-Bausteine (`AX_CLIENT_1_0` in den Composites, 2x `AX_SUBSCRIBE_1`) und der Interlock mit Schutzzeit auf Gerät B.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
