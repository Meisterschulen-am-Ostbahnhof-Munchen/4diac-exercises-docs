# Uebung_075a_AUI: FPTO Speed + SpeedSet auf UT ausgeben mit AR_Timeout_Zero (Adapter Version)

![Uebung_075a_AUI_network](./Uebung_075a_AUI_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung gibt die Drehzahl (**Speed**) und die Solldrehzahl (**SpeedSet**) der Front-Zapfwelle (FPTO, Front Power Take-off) des TECU auf einem Universal Terminal (UT) aus. Beide Rohwerte kommen über den Adapter `IA_FPTO`, werden mit `0.125` skaliert und über `Q_NumericValue_PHYSA` angezeigt. Für jeden Wert sorgt eine Instanz der "Fendt-Schaltung" `AR_Timeout_Zero` dafür, dass bei einem Kommunikationsausfall `0.0` statt des eingefrorenen letzten Werts angezeigt wird. Die Übung ist analog zu `TECU_FPTO.SUB` der Krauternter-Software.

## Verwendete Funktionsbausteine (FBs)

- **IA_FPTO**: ISOBUS-Adapter für die Front-Zapfwelle
    - **Typ**: `isobus::tecu::IA_FPTO`
    - **Parameter**: `QI = TRUE`
    - **Erklärung**: Liefert die UINT-Adapter `SPEED` (Drehzahl der Abtriebswelle) und `SPEED_SET` (Soll-Drehzahl) sowie den Adapter `TIMEOUT` (AX, TRUE bei Kommunikationsausfall).
- **AX_SPLIT_2_Timeout**: Verteiler für den Timeout-Status
    - **Typ**: `adapter::events::unidirectional::AX_SPLIT_2`
    - **Parameter**: keine
    - **Erklärung**: Verteilt `IA_FPTO.TIMEOUT` auf `OUT1` (→ `TZ_Speed.G`) und `OUT2` (→ `TZ_SpeedSet.G`).
- **Conv_Speed**, **Conv_SpeedSet**: Skalierung der Rohwerte
    - **Typ**: `logiBUS::signalprocessing::fieldbus::AUI_FIELDBUS_UINT_TO_SIGNAL_SCALED`
    - **Parameter**: je `SCALE = 0.125`, `OFFSET = 0`
    - **Erklärung**: Wandeln den UINT-Rohwert (`IN`) in einen skalierten REAL-Wert (`OUT`, AR) um.
- **TZ_Speed**, **TZ_SpeedSet**: Timeout-Nullung (SubApp-Instanzen)
    - **Typ**: `MyLib::sys::AR_Timeout_Zero`
    - **Parameter**: keine
    - **Erklärung**: Liefern `IN0` unverändert an `OUT`, außer `G` ist TRUE (Timeout) - dann `0.0`.
- **Q_NumericValue_FPTO_Speed**: Ausgabe der Drehzahl
    - **Typ**: `isobus::UT::Q::Q_NumericValue_PHYSA`
    - **Parameter**: `stObj = NumberVariable_TECU_FPTO_Speed_N`
    - **Erklärung**: Schreibt den physikalischen Wert (`rPhys`) in das zugehörige VT-Objekt.
- **Q_NumericValue_FPTO_SpeedSet**: Ausgabe der Solldrehzahl
    - **Typ**: `isobus::UT::Q::Q_NumericValue_PHYSA`
    - **Parameter**: `stObj = NumberVariable_TECU_FPTO_SpeedSet_N`
    - **Erklärung**: Schreibt den physikalischen Wert (`rPhys`) in das zugehörige VT-Objekt.

## Programmablauf und Verbindungen

1. Drehzahl: `IA_FPTO.SPEED` → `Conv_Speed.IN` → `TZ_Speed.IN0`; `AX_SPLIT_2_Timeout.OUT1` → `TZ_Speed.G`; `TZ_Speed.OUT` → `Q_NumericValue_FPTO_Speed.rPhys`.
2. Solldrehzahl: `IA_FPTO.SPEED_SET` → `Conv_SpeedSet.IN` → `TZ_SpeedSet.IN0`; `AX_SPLIT_2_Timeout.OUT2` → `TZ_SpeedSet.G`; `TZ_SpeedSet.OUT` → `Q_NumericValue_FPTO_SpeedSet.rPhys`.
3. `IA_FPTO.TIMEOUT` → `AX_SPLIT_2_Timeout.IN`: ein einziger Timeout-Status steuert beide Nullungen.

**Verhalten**: Solange Daten eintreffen, zeigt das UT Drehzahl und Solldrehzahl (je Rohwert x 0.125). Bei Kommunikationsausfall (`TIMEOUT` = TRUE) zeigen beide Felder `0.0`.

## Zusammenfassung

Die Übung erweitert die Signalkette aus `Uebung_073d_AUI` auf zwei Messwerte desselben ISOBUS-Adapters: Der gemeinsame Timeout-Status wird mit `AX_SPLIT_2` auf zwei `AR_Timeout_Zero`-Instanzen verteilt, sodass beide Anzeigen konsistent auf `0.0` fallen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
