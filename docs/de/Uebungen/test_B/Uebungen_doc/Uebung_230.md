# Uebung_230: Analog-Eingang

![Uebung_230_network](./Uebung_230_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung liest einen analogen 0-10V-Eingang auf dem DataPanel ein und wandelt den rohen DWORD-Messwert in einen nutzbaren UDINT-Zahlenwert um, gefiltert über eine Hysterese gegen Signalrauschen.

## Verwendete Funktionsbausteine (FBs)

- **DataPanel_MI_IW_0_10V**: Analoger DataPanel-Eingang (Typ: `DataPanel::io::MI::AI::DataPanel_MI_IW_0_10V`)
    - **Parameter**: `QI = TRUE`, `u8SAMember = MI_00`, `Input = AnalogInput_5B`, `AnalogInput_hysteresis = 50`
    - **Erklärung**: Liest laufend die analoge Eingangsspannung (0-10V) auf dem DataPanel-Erweiterungsmodul ein, gefiltert über eine Hysterese von 50, um kleine Signalschwankungen zu unterdrücken.
- **F_DWORD_TO_UDINT_I8**: Typkonvertierung (Typ: `iec61131::conversion::F_DWORD_TO_UDINT`)
    - **Parameter**: keine
    - **Erklärung**: Wandelt den rohen DWORD-Messwert in einen UDINT-Zahlenwert um, der für die Weiterverarbeitung nutzbar ist.

### Sub-Bausteine: keine

## Programmablauf und Verbindungen

1. `DataPanel_MI_IW_0_10V` liest laufend die analoge Eingangsspannung ein.
2. Sowohl bei jeder Wertänderung (`IND`) als auch bei jeder Bestätigung (`CNF`) wird `F_DWORD_TO_UDINT_I8.REQ` ausgelöst.
3. Der Datenwert `DataPanel_MI_IW_0_10V.IN` wird auf `F_DWORD_TO_UDINT_I8.IN` geführt.
4. `F_DWORD_TO_UDINT_I8` wandelt den DWORD-Rohwert in einen UDINT-Zahlenwert um.

**Hinweis zur Hardware**: Ein Kommentar im Netzwerk warnt ausdrücklich: Nicht jedes X-Board besitzt zwangsläufig die Analogeingänge `5A`-`8B`/`1A`-`4B` – manche Boards haben nur `5B` und `6B` als Analogeingang, während `5A` und `6A` dort rein digital sind. Vor dem Einsatz dieser Übung sollte daher die tatsächliche Bestückung des verwendeten Boards geprüft werden.

## Zusammenfassung

Die Übung zeigt die einfachste Form der Analogwertverarbeitung auf dem DataPanel: Spannungseingang mit Hysterese einlesen und in einen numerisch nutzbaren Datentyp konvertieren. Wichtig ist dabei die Beachtung der tatsächlichen Board-Bestückung, da nicht alle DataPanel-Erweiterungsmodule dieselben Kanäle als Analogeingänge anbieten.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
