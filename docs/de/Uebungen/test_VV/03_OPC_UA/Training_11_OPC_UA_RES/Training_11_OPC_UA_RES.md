# Training_11_OPC_UA_RES: Hoch/Runter-Schalter über OPC-UA — VT-SoftKeys mit Rückmeldung

![Training_11_OPC_UA_RES_network](./Training_11_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_11_OPC_UA_RES` ersetzt gegenüber `Training_10_OPC_UA_RES` die physischen `logiBUS_IXA`-Eingänge durch zwei VT-SoftKeys ([`Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/), `SoftKey_F1` = Hoch, `SoftKey_F2` = Runter). Gerät A schreibt sie aktiv per OPC-UA (`CLIENT_1_0`) auf die von Gerät B überwachten Knoten. Gerät B ist identisch zu Training_10 (2x `AX_SUBSCRIBE_1` → `ILOCK_SWITCH_PROTECT_AX` → 2x `logiBUS_QXA`) und schreibt zusätzlich den tatsächlich erreichten Zustand (`UP_OUT`/`DOWN_OUT` nach Arbitrierung/Schutzzeit) per `AX_SPLIT_2` + `AX_CLIENT_1_0` zurück an Gerät A.

Auf Gerät A färbt je ein [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) den jeweiligen SoftKey nach diesem zurückgemeldeten Zustand — also nicht nach dem rohen Tastendruck. `Softkey_IXA` liest kontinuierlich (wie `logiBUS_IXA`), ein REQ-Polling ist nicht nötig; `INIT` (`EInit`) startet die Überwachung automatisch.

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_11_network.svg) | A (`192.168.1.11`) | 2x [`Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/) (`UpKey`: `SoftKey_F1`; `DownKey`: `SoftKey_F2`), 2x [`AX_CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_CLIENT_1_0/) (Schreiben), 2x [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/) (Rückmeldung lesen), 2x [`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/) (`BG_Up`, `BG_Down`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_11_network.svg) | B (`192.168.1.12`) | 2x [`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/), [`ILOCK_SWITCH_PROTECT_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_AX/), [`E_TimeOut`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_TimeOut/), 2x [`AX_SPLIT_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_SPLIT_2/), 2x [`logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/), 2x [`AX_CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_CLIENT_1_0/) (Rückmeldung schreiben) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten (Schreiben auf anderer Station / lokal lesen) | Knoten | Bedeutung |
|---|---|---|
| `SW1_UP_REMOTE_WRITE / SW1_UP_LOCAL_READ` | `/Objects/Switch1/Up,1:s=Switch1_Up` | Knoten auf B; A schreibt, B liest lokal |
| `SW1_DOWN_REMOTE_WRITE / SW1_DOWN_LOCAL_READ` | `/Objects/Switch1/Down,1:s=Switch1_Down` | Knoten auf B; A schreibt, B liest lokal |
| `SW1_UP_STATE_REMOTE_WRITE / SW1_UP_STATE_LOCAL_READ` | `/Objects/Switch1/UpState,1:s=Switch1_UpState` | Knoten auf A; B schreibt, A liest lokal |
| `SW1_DOWN_STATE_REMOTE_WRITE / SW1_DOWN_STATE_LOCAL_READ` | `/Objects/Switch1/DownState,1:s=Switch1_DownState` | Knoten auf A; B schreibt, A liest lokal |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A), Adapterverbindungen:

    1. `UpKey.IN` → `UpTasterClient.IN`
    2. `DownKey.IN` → `DownTasterClient.IN`
    3. `StateUpSubscribe.OUT` → `BG_Up.DI1`
    4. `StateDownSubscribe.OUT` → `BG_Down.DI1`

2. **`SubApp_PC_B`** (Gerät B), Adapterverbindungen:

    1. `SUBSCRIBE_UP.OUT` → `ILOCK_AX.UP_IN`
    2. `SUBSCRIBE_DOWN.OUT` → `ILOCK_AX.DOWN_IN`
    3. `ILOCK_AX.timeOut` → `E_TimeOut.TimeOutSocket`
    4. `ILOCK_AX.UP_OUT` → `SPLIT_UP.IN`
    5. `SPLIT_UP.OUT1` → `DigitalOutput_Q7.OUT`
    6. `SPLIT_UP.OUT2` → `StateUpClient.IN`
    7. `ILOCK_AX.DOWN_OUT` → `SPLIT_DOWN.IN`
    8. `SPLIT_DOWN.OUT1` → `DigitalOutput_Q8.OUT`
    9. `SPLIT_DOWN.OUT2` → `StateDownClient.IN`

**Parameter**: Gerät A: `UpKey`/`DownKey` ([`Softkey_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/isobus/UT/io/Softkey/Softkey_IXA/)): `QI` = `TRUE`, `u16ObjId` = `SoftKey_F1` bzw. `SoftKey_F2`; `UpTasterClient`/`DownTasterClient` ([`AX_CLIENT_1_0`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_CLIENT_1_0/)): `QI` = `TRUE`, `ID` = `SW1_UP_REMOTE_WRITE` bzw. `SW1_DOWN_REMOTE_WRITE`; `StateUpSubscribe`/`StateDownSubscribe` ([`AX_SUBSCRIBE_1`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/AX_SUBSCRIBE_1/)): `QI` = `TRUE`, `ID` = `SW1_UP_STATE_LOCAL_READ` bzw. `SW1_DOWN_STATE_LOCAL_READ`; `BG_Up`/`BG_Down` ([`GreenWhiteBackground1_AX`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/GreenWhiteBackground1_AX/)): `u16ObjId` = `SoftKey_F1` bzw. `SoftKey_F2`. Gerät B: `SUBSCRIBE_UP`/`SUBSCRIBE_DOWN`: `QI` = `TRUE`, `ID` = `SW1_UP_LOCAL_READ` bzw. `SW1_DOWN_LOCAL_READ`; `ILOCK_AX`: `DT_PROTECT` = `T#1s`; `DigitalOutput_Q7`/`DigitalOutput_Q8`: `QI` = `TRUE`, `Output` = `Output_Q7` bzw. `Output_Q8`; `StateUpClient`/`StateDownClient`: `QI` = `TRUE`, `ID` = `SW1_UP_STATE_REMOTE_WRITE` bzw. `SW1_DOWN_STATE_REMOTE_WRITE`.

## Technische Besonderheiten

- **Rückmeldung statt Tastendruck**: Die SoftKeys werden nach dem von Gerät B tatsächlich erreichten Zustand (nach ILOCK-Arbitrierung/Schutzzeit) gefärbt, nicht nach dem rohen Tastendruck.
- **State-Write von B nach A**: Gleiches Prinzip wie im Vorbild `SoftKeySR_PC_A_OPC` — Gerät B schreibt per `AX_CLIENT_1_0` auf einen lokal von Gerät A überwachten Knoten (`*_STATE_*`).
- **Verteilung per `AX_SPLIT_2`**: Der interlockte Zustand geht parallel an den physischen Ausgang und an den Rückmelde-Client.
- **Kontinuierliches Lesen**: `Softkey_IXA` braucht kein REQ-Polling (Vorbild `Softkey_IXA_TO_logiBUS_QXA_BG`).

## Lernziele

- VT-SoftKeys (`Softkey_IXA`) als OPC-UA-Eingabe statt physischer Eingänge.
- Bestätigten Zustand zurückschreiben (B → A) und per `GreenWhiteBackground1_AX` anzeigen.
- Interlock-Ausgang per `AX_SPLIT_2` auf Ausgang und Rückmeldung verteilen.

**Schwierigkeitsgrad**: Fortgeschritten  
**Vorkenntnisse**: `Training_10_OPC_UA_RES`.

## Zusammenfassung

`Training_11_OPC_UA_RES` erweitert Training_10 um VT-SoftKeys und eine Rückmeldung: Gerät B meldet den nach Interlock und Schutzzeit tatsächlich erreichten Zustand an Gerät A zurück, wo er den SoftKey einfärbt.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
