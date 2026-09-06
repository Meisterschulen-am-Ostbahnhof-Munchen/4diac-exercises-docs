# InputOutputTesterButton_PI_OPC_UA: PI Tester (OPC-UA)

![InputOutputTesterButton_PI_OPC_UA_network](./InputOutputTesterButton_PI_OPC_UA_network.svg)

* * * * * * * * * *

## Einleitung

`InputOutputTesterButton_PI_OPC_UA` ist das Trainingsbeispiel für **8 Puls-Eingänge (Zähler + Frequenz + Bargraph)**, steuerbar sowohl über den ISOBUS-Virtual-Terminal als auch über OPC-UA. Die 12 digitalen Ausgänge sind unverändert aus [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) übernommen ("PI" = **P**ulse **I**nput, Impulseingang für Frequenzmessung — nicht zu verwechseln mit den analogen `AI`-Kanälen).

Jeder Pulskanal hat zusätzlich einen **eigenen Enable-Schalter mit Status-LED** — anders als bei DIDO/AI/AI_Calibrate ist hier also nicht nur der Messwert, sondern die Aktivierung des Kanals selbst bidirektional von VT und Web aus änderbar.

## Verwendete Funktionsbausteine (FBs)

| SubApp-Instanz | Typ | Zweck |
|---|---|---|
| `PulseChannel_I1` … `PulseChannel_I8` | `MyLib::sys::logiBUS_PI_IDA_OPC` | Pulseingang mit Zähler- und Frequenz-Anzeige (VT-Zahlenfelder + Bargraph), Enable-Schalter mit Status-LED, + OPC-UA |
| `Output_Q1` … `Output_Q12` | `MyLib::sys::Button_IXA_TO_logiBUS_QXA_BG_OPC` | Digitaler Ausgang, unverändert aus dem DIDO-Beispiel übernommen |

### Sub-Baustein: `logiBUS_PI_IDA_OPC` (Pulseingänge)

- **Typ**: SubAppType (`MyLib::sys`)
- **Funktionsweise**: Der physische Pulseingang (`logiBUS_PI`) liefert sowohl einen fortlaufenden Impulszähler (`COUNTVAR`, VT-Zahlenfeld) als auch die daraus abgeleitete Frequenz (`stObjFreq`, VT-Zahlenfeld mit Bargraph) — beide zusätzlich per OPC-UA veröffentlicht (`ID_COUNT_WRITE`, `ID_FREQ_WRITE`).
- **Enable-Schalter mit Status-Rückmeldung**: Jeder Kanal hat einen eigenen Enable-Schalter (`u16ObjId_SWITCH`, per VT-Taste **und** OPC-UA schaltbar über `ID_SWITCH_READ`/`ID_SWITCH_WRITE`) sowie eine Status-LED (`u16ObjId_STATUS`, per OPC-UA veröffentlicht über `ID_STATUS_WRITE`), die den tatsächlichen Aktivierungszustand des Kanals zurückmeldet — dasselbe Set/Reset-plus-Echo-Muster wie bei den bidirektional schaltbaren Ausgängen der anderen Beispiele, hier aber auf einen Eingangs-Freigabeschalter angewendet.
- **Default-Aktivierung pro Kanal**: `bDefaultEnabled` ist für die Kanäle 1–4 auf `TRUE`, für die Kanäle 5–8 auf `FALSE` voreingestellt — die zweite Kanalgruppe muss beim Start erst aktiv geschaltet werden, bevor sie zählt.

### Sub-Baustein: [Button_IXA_TO_logiBUS_QXA_BG_OPC](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs-en/en/latest/ExternalLibraries/MyLib_AX/sys/Button_IXA_TO_logiBUS_QXA_BG_OPC/) (Ausgänge)

Unverändert aus dem DIDO-Beispiel — siehe dortige Beschreibung.

## OPC-UA-Adressraum

| Node-Pfad | Node-ID | Bedeutung |
|---|---|---|
| `/Objects/Pulse/In/COUNT` | `s=PI_In_COUNT` | Impulszähler Eingang n (n=1–8), nur Publish |
| `/Objects/Pulse/In/FREQ` | `s=PI_In_FREQ` | Frequenz Eingang n (n=1–8), nur Publish |
| `/Objects/Pulse/In/SWITCH` | `s=PI_In_SWITCH` | Enable-Schalter Eingang n, Read (Subscribe) + Write (Publish/Echo) |
| `/Objects/Pulse/In/STATUS` | `s=PI_In_STATUS` | Status-LED Eingang n, nur Publish |
| `/Objects/DigitalOutput/Qnn` | `s=Qnn` | Ausgang nn (nn=01–12), wie im DIDO-Beispiel |

## Programmablauf und Verbindungen

Die Übung selbst enthält **keine Verbindungen** (`SubAppNetwork` besteht nur aus SubApp-Instanzen mit Parametern):

1. **8 Pulskanäle**: `PulseChannel_I1`…`PulseChannel_I8` lesen `PulseInput_I1`…`PulseInput_I8`, zählen und messen deren Frequenz, veröffentlichen beides per OPC-UA und lassen sich einzeln per VT-Taste oder OPC-UA aktivieren/deaktivieren (Kanäle 1–4 initial aktiv, 5–8 initial inaktiv).
2. **12 digitale Ausgänge**: `Output_Q1`…`Output_Q12`, unverändert aus dem DIDO-Beispiel.

**Registrierung im Trainingssystem**: Wie bei allen Übungen in diesem System kein eigenes `Application`-Element nötig — Auswahl per "Change Type" im 4diac IDE auf den einen `Control`-Slot des `System`.

## Lernziele

- Impulszählung und Frequenzmessung als eigener Analogeingang-Typ, abzugrenzen von den spannungsbasierten `AI`-Kanälen.
- Bidirektional schaltbare Kanal-Freigabe mit Status-Rückmeldung (Enable/Status-Paar) als wiederverwendbares Muster für "Kanal ein-/ausschaltbar machen" über VT und OPC-UA gleichzeitig.

**Schwierigkeitsgrad**: Mittel
**Vorkenntnisse**: [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) (Grundmuster VT+OPC-UA), [`InputOutputTesterButton_AI_OPC_UA`](../Button_AI_OPC_UA/InputOutputTesterButton_AI_OPC_UA.md) (Vergleich zu einem einfacheren Analogeingang ohne Enable-Schalter).

## Zusammenfassung

`InputOutputTesterButton_PI_OPC_UA` demonstriert Impulszählung mit Frequenzmessung und einem zusätzlichen, bidirektional schaltbaren Enable/Status-Paar je Kanal — eine Erweiterung des einfachen Publish-Musters (wie bei AI) um eine echte Rückkopplungslogik auf Eingangsseite.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
