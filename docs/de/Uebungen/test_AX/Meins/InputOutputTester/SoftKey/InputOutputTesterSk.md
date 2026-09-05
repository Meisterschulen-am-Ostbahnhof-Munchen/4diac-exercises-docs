# InputOutputTesterSk: DIDO Tester (VT, SoftKey-Ausgänge)

![InputOutputTesterSk_network](./InputOutputTesterSk_network.svg)

* * * * * * * * * *

## Einleitung

`InputOutputTesterSk` ist strukturell identisch zu [`InputOutputTesterBt`](../Button/InputOutputTesterBt.md) (8 digitale Eingänge, 12 digitale Ausgänge, rein VT-basiert, keine OPC-UA-Anbindung) — der einzige Unterschied ist der Ausgangsbaustein: Die 12 Ausgänge werden hier über feste VT-**SoftKeys** (physische Tasten neben dem Bildschirm) geschaltet statt über in die DataMask eingebettete on-screen-Taster (`Button_IXA`).

## Verwendete Funktionsbausteine (FBs)

| SubApp-Instanz | Typ | Zweck |
|---|---|---|
| `Input_I1` … `Input_I8` | `MyLib::sys::logiBUS_IXA_BG` | Digitaler Eingang mit VT-Statusanzeige (Grün/Weiß) — unverändert aus `InputOutputTesterBt` |
| `Output_Q1` … `Output_Q12` | `MyLib::sys::Softkey_IXA_TO_logiBUS_QXA_BG` | Digitaler Ausgang, schaltbar über einen **VT-SoftKey** statt eines on-screen-Tasters |

### Sub-Baustein: `Softkey_IXA_TO_logiBUS_QXA_BG` (Ausgänge)

- **Typ**: SubAppType (`MyLib::sys`)
- **Funktionsweise**: Wie `Button_IXA_TO_logiBUS_QXA_BG` (AX_SR-Flipflop schaltet physischen Ausgang + VT-Statusfarbe), aber ausgelöst über eine `Softkey_IXA`-Verbindung (VT-SoftKeyMask-Taste) statt einer `Button_IXA`-Verbindung (VT-DataMask-Taster). Didaktisch relevanter Unterschied: SoftKeys sind maskenübergreifend fest positioniert (physische Tasten am VT-Gerät), während DataMask-Buttons Teil des jeweils angezeigten Bildschirminhalts sind.

## Programmablauf und Verbindungen

Die Übung selbst enthält **keine Verbindungen** (`SubAppNetwork` besteht nur aus SubApp-Instanzen mit Parametern) — identisch zu `InputOutputTesterBt`:

1. **8 Eingänge**: `Input_I1`…`Input_I8` lesen `Input_I1`…`Input_I8` und spiegeln sie per VT-Statusfarbe.
2. **12 Ausgänge**: `Output_Q1`…`Output_Q12` verbinden je einen physischen Ausgang mit einem VT-SoftKey und VT-Statusfarbe.

**Registrierung im Trainingssystem**: Wie bei allen Übungen in diesem System kein eigenes `Application`-Element nötig — Auswahl per "Change Type" im 4diac IDE auf den einen `Control`-Slot des `System`.

## Lernziele

- Unterschied zwischen SoftKey- und DataMask-Button-Bedienung am selben Grundmuster (digitale I/O), ohne dass sich sonst etwas an der Logik ändert.
- SoftKeys als Alternative für Bedienelemente, die unabhängig vom aktuell angezeigten Maskeninhalt erreichbar sein müssen.

**Schwierigkeitsgrad**: Einsteiger
**Vorkenntnisse**: [`InputOutputTesterBt`](../Button/InputOutputTesterBt.md) (identisches Grundmuster mit DataMask-Buttons), Grundlagen der ISOBUS-VT-SoftKeyMasken.

## Zusammenfassung

`InputOutputTesterSk` zeigt dasselbe digitale 8+12-I/O-Grundmuster wie `InputOutputTesterBt`, gesteuert über feste VT-SoftKeys statt on-screen-Buttons — eine reine Bedienkonzept-Variante ohne Änderung an der zugrundeliegenden FB-Logik.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
