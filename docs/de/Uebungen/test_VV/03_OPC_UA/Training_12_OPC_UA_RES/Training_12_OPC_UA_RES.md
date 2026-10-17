# Training_12_OPC_UA_RES: Hoch/Runter-Schalter über OPC-UA — gebündelte A2X-Transportschicht

![Training_12_OPC_UA_RES_network](./Training_12_OPC_UA_RES_network.svg)

* * * * * * * * * *

## Einleitung

`Training_12_OPC_UA_RES` entspricht funktional `Training_11_OPC_UA_RES`, bündelt aber die Transportschicht: Statt je 2x `AX_SUBSCRIBE_1`/`AX_CLIENT_1_0` (4 einzelne Netzwerk-Bausteine, 4 Konstanten) übernimmt **ein** Baustein den kompletten Roundtrip für Hoch und Runter (je ein `CLIENT_2_0`/`SUBSCRIBE_2`, 2 Werte pro Aufruf, 2 Konstanten).

- Gerät A: die 4 lokalen Bausteine (2 SoftKeys + 2 `GreenWhiteBackground`) stecken gebündelt in [`Softkey2_TO_A2X2_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey2_TO_A2X2_BG/), das nach außen nur einen einzigen `A2X2`-Plug zeigt; dieser wird direkt in [`A2X2_CLIENT_2_0_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2/) gesteckt.
- Gerät B: [`A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG/) übernimmt den Roundtrip. [`A2X2_ILOCK_SWITCH_PROTECT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X2_ILOCK_SWITCH_PROTECT/) kapselt Bridge (`A2X2_TO_A2X`), Interlock ([`ILOCK_SWITCH_PROTECT_A2X`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/signalprocessing/interlock/ILOCK_SWITCH_PROTECT_A2X/)) und Split (`A2X_SPLIT_2`) in einem Composite (`DT_PROTECT` als Parameter) und liefert den interlockten Zustand als `A2X`-Plug. [`A2X_TO_QXA2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X_TO_QXA2/) entbündelt auf die 2 physischen `logiBUS_QXA`-Ausgänge.

Die Daten bleiben somit `A2X`-gebündelt bis unmittelbar vor der physischen Hardware.

## Systemaufbau

Gerät A (`FORTE_PC_A`, `MGR_ID` = `PC_A`, Station 11, `192.168.1.11`) mit `EMB_RES_A`, Gerät B (`FORTE_PC_B`, `MGR_ID` = `PC_B`, Station 12, `192.168.1.12`) mit `EMB_RES_B`, verbunden über das Segment `Ethernet`. Jede Resource enthält nur `E_TRIG` (`EVENTTYPE` = `'EInit'`), das von `START.COLD` angestoßen wird; die gesamte Logik steckt in den SubApps.

## Verwendete SubApps

| SubApp | Gerät | Inhalt |
|---|---|---|
| [`SubApp_PC_A`](./SubApp_PC_A_Training_12_network.svg) | A (`192.168.1.11`) | `Keys` ([`Softkey2_TO_A2X2_BG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/Softkey2_TO_A2X2_BG/): `u16ObjId_UP` = `SoftKey_F1`, `u16ObjId_DOWN` = `SoftKey_F2`), `NET` ([`A2X2_CLIENT_2_0_SUBSCRIBE_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2/): `QI` = `TRUE`, `ID_WRITE` = `SW2_PRESS_REMOTE_WRITE`, `ID_READ` = `SW2_STATE_LOCAL_READ`) |
| [`SubApp_PC_B`](./SubApp_PC_B_Training_12_network.svg) | B (`192.168.1.12`) | `NET` ([`A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/net/BOOL/A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG/): `QI` = `TRUE`, `ID_WRITE` = `SW2_STATE_REMOTE_WRITE`, `ID_READ` = `SW2_PRESS_LOCAL_READ`), `ILOCK` ([`A2X2_ILOCK_SWITCH_PROTECT`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X2_ILOCK_SWITCH_PROTECT/): `DT_PROTECT` = `T#1s`), `Outputs` ([`A2X_TO_QXA2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/A2X_TO_QXA2/): `Output_UP` = `Output_Q7`, `Output_DOWN` = `Output_Q8`) |

## OPC-UA-Adressraum

Die IDs stammen aus `myOpcUaAddresses.gcf`; die Stationen `PC_A`/`PC_B` stehen nur dort, die OPC-UA-IDs werden mit den Functions der Lib `MyLib_OPC` berechnet. Aufbau und Berechnung: [OPC-UA-Adressen](../OPC_UA_Adressen.md).

| Konstanten (Schreiben auf anderer Station / lokal lesen) | Knoten | Bedeutung |
|---|---|---|
| `SW2_PRESS_REMOTE_WRITE / SW2_PRESS_LOCAL_READ` | `/Objects/Switch2/Up,1:s=Switch2_Up;/Objects/Switch2/Down,1:s=Switch2_Down` | Zwei Knoten in einem String (`;`), auf B; A schreibt, B liest lokal |
| `SW2_STATE_REMOTE_WRITE / SW2_STATE_LOCAL_READ` | `/Objects/Switch2/UpState,1:s=Switch2_UpState;/Objects/Switch2/DownState,1:s=Switch2_DownState` | Zwei Knoten in einem String (`;`), auf A; B schreibt, A liest lokal |

## Programmablauf und Verbindungen

1. **`SubApp_PC_A`** (Gerät A), Adapterverbindung:

    1. `Keys.OUT` → `NET.IO`

2. **`SubApp_PC_B`** (Gerät B), Adapterverbindungen:

    1. `NET.IO` → `ILOCK.IO`
    2. `ILOCK.OUT` → `Outputs.IN`

## Technische Besonderheiten

- **Zwei Konstanten statt vier**: `SW2_*` bündelt zwei Knoten in einem String (mit `;` getrennt) für `SUBSCRIBE_2`/`CLIENT_2_0` (siehe [OPC-UA-Adressen](../OPC_UA_Adressen.md)).
- **A2X-Bündelung bis zur Hardware**: Auf Gerät B bleibt die Kette Bridge → Interlock → Split in `A2X2_ILOCK_SWITCH_PROTECT` gekapselt; erst `A2X_TO_QXA2` entbündelt auf die physischen Ausgänge.
- **`SUBSCRIBE_2` statt Methodenaufruf**: Ein einfacher OPC-UA-Methodenaufruf (`CLIENT_2`/`SERVER_2`, Request/Response) wurde für den gebündelten Roundtrip verworfen — der Remote-Zustand kann sich unabhängig von den eigenen Schreibzugriffen aus einer dritten Quelle ändern, und die lokale Oberfläche muss auf diesen Push reagieren, was nur `SUBSCRIBE_2` leistet (Begründung im Commit `04b465d77`).
- **Plug und Socket**: Gerät A nutzt den Socket-Baustein `A2X2_CLIENT_2_0_SUBSCRIBE_2`, Gerät B die Plug-Variante `A2X2_CLIENT_2_0_SUBSCRIBE_2_PLUG`.

## Lernziele

- Mehrere Werte pro OPC-UA-Aufruf bündeln (`A2X2_CLIENT_2_0_SUBSCRIBE_2`, 2 Konstanten statt 4).
- Bündelnde Composites (`Softkey2_TO_A2X2_BG`, `A2X2_ILOCK_SWITCH_PROTECT`, `A2X_TO_QXA2`) und die A2X-Variante des Interlocks (`ILOCK_SWITCH_PROTECT_A2X`) einsetzen.
- Unterschied Plug-/Socket-Variante des Netzwerkbausteins verstehen.

**Schwierigkeitsgrad**: Fortgeschritten  
**Empfohlene Reihenfolge**: `Training_11_OPC_UA_RES`.

## Zusammenfassung

`Training_12_OPC_UA_RES` ist die gebündelte Variante von Training_11: dieselbe Funktion, aber ein einziger `A2X2`-Roundtrip je Gerät, `A2X`-gebündelt bis unmittelbar vor SoftKeys bzw. physischen Ausgängen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
