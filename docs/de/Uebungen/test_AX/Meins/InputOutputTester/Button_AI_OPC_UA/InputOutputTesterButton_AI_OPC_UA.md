# InputOutputTesterButton_AI_OPC_UA: AI Tester (OPC-UA, ohne Kalibrierung)

![InputOutputTesterButton_AI_OPC_UA_network](./InputOutputTesterButton_AI_OPC_UA_network.svg)

* * * * * * * * * *

## Einleitung

`InputOutputTesterButton_AI_OPC_UA` ist das Trainingsbeispiel für **8 analoge Eingänge (Rohwert + Prozent + Bargraph)**, steuerbar sowohl über den ISOBUS-Virtual-Terminal als auch über OPC-UA. Die 12 digitalen Ausgänge sind unverändert aus [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) übernommen.

Diese Übung ist die **einfachere Vorstufe** zu [`InputOutputTesterButton_AI_Calibrate_OPC_UA`](../Button_AI_Calibrate_OPC_UA/InputOutputTesterButton_AI_Calibrate_OPC_UA.md): Sie zeigt Rohwert und (linear umgerechneten) Prozentwert eines Analogeingangs, aber **ohne** die spätere 2-Punkt-Kalibrierlogik (kein `CALIBRATE`-Adapter, keine CO-/CS-Tasten, keine INI-persistierten Offset/Scale-Werte, keine remote setzbaren ZERO-/SPAN-Referenzen). Wer verstehen will, was die Kalibrier-Erweiterung an FB-Netzwerk hinzufügt, vergleicht diese beiden Übungen am besten direkt.

## Verwendete Funktionsbausteine (FBs)

| SubApp-Instanz | Typ | Zweck |
|---|---|---|
| `AnalogChannel_I1` … `AnalogChannel_I8` | `MyLib::sys::logiBUS_AI_IDA_OPC` | Analogeingang mit Rohwert- und Prozent-Anzeige (VT-Zahlenfelder + Bargraph) + OPC-UA, **ohne** Kalibrierung |
| `Output_Q1` … `Output_Q12` | `MyLib::sys::Button_IXA_TO_logiBUS_QXA_BG_OPC` | Digitaler Ausgang, unverändert aus dem DIDO-Beispiel übernommen |

### Sub-Baustein: `logiBUS_AI_IDA_OPC` (Analogeingänge)

- **Typ**: SubAppType (`MyLib::sys`)
- **Funktionsweise**: Der physische Analogeingang (`logiBUS_AI_IDA`) liefert Rohwert und linear umgerechneten Prozentwert direkt an je ein VT-Zahlenfeld mit Bargraph sowie per OPC-UA-Publish (`ID_RAW_WRITE`, `ID_PERCENT_WRITE`) an den Web-Client. Anders als bei der Calibrate-Variante gibt es keine adaptive Kalibrierkette (`AR_CALIBRATE_SQ_REF`), keine Referenzwerte und keine remote auslösbaren Kalibrierschritte — der Baustein ist reiner Publish-Pfad ohne Rückschreibmöglichkeit.

### Sub-Baustein: [Button_IXA_TO_logiBUS_QXA_BG_OPC](https://docs.ms-muc-docs.de/projects/4diac-library-reference-docs/en/latest/ExternalLibraries/MyLib_AX/sys/Button_IXA_TO_logiBUS_QXA_BG_OPC/) (Ausgänge)

Unverändert aus dem DIDO-Beispiel — siehe dortige Beschreibung.

## OPC-UA-Adressraum

| Node-Pfad | Node-ID | Bedeutung |
|---|---|---|
| `/Objects/Analog/In/RAW` | `s=AI_In_RAW` | Rohwert Eingang n (n=1–8), nur Publish |
| `/Objects/Analog/In/PERCENT` | `s=AI_In_PERCENT` | Prozentwert Eingang n (n=1–8), nur Publish |
| `/Objects/DigitalOutput/Qnn` | `s=Qnn` | Ausgang nn (nn=01–12), Read (Subscribe) + Write (Publish/Echo), wie im DIDO-Beispiel |

Flacher, nach Kanal getrennter Adressraum, kein verschachtelter Pfad wie beim PWM-Beispiel — analog zum DIDO-Adressraum.

## Programmablauf und Verbindungen

Die Übung selbst enthält **keine Verbindungen** (`SubAppNetwork` besteht nur aus SubApp-Instanzen mit Parametern):

1. **8 Analogkanäle**: `AnalogChannel_I1`…`AnalogChannel_I8` lesen `AnalogInput_I1`…`AnalogInput_I8` und veröffentlichen Rohwert + Prozentwert per OPC-UA.
2. **12 digitale Ausgänge**: `Output_Q1`…`Output_Q12`, unverändert aus dem DIDO-Beispiel.

**Registrierung im Trainingssystem**: Wie bei allen Übungen in diesem System kein eigenes `Application`-Element nötig — Auswahl per "Change Type" im 4diac IDE auf den einen `Control`-Slot des `System`.

## Lernziele

- Grundmuster für einen reinen Publish-Analogeingang (Rohwert + Prozent) mit VT- **und** OPC-UA-Anbindung, ohne die Komplexität einer Kalibrierlogik.
- Direkter Vergleich mit [`InputOutputTesterButton_AI_Calibrate_OPC_UA`](../Button_AI_Calibrate_OPC_UA/InputOutputTesterButton_AI_Calibrate_OPC_UA.md) zeigt konkret, welche zusätzlichen Bausteine (CALIBRATE-Adapter, INI-Persistenz, Referenzwert-Tasten) eine 2-Punkt-Kalibrierung erfordert.

**Schwierigkeitsgrad**: Einsteiger bis Mittel
**Vorkenntnisse**: [`InputOutputTesterButton_DIDO_OPC_UA`](../Button_DIDO_OPC_UA/InputOutputTesterButton_DIDO_OPC_UA.md) (Grundmuster VT+OPC-UA).

## Zusammenfassung

`InputOutputTesterButton_AI_OPC_UA` demonstriert die einfachste Form eines analogen OPC-UA-Eingangs: Rohwert und Prozentwert, rein per Publish, ohne Kalibrierlogik. Bildet die didaktische Brücke zwischen dem rein digitalen DIDO-Beispiel und der deutlich komplexeren 2-Punkt-Kalibrierung.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
