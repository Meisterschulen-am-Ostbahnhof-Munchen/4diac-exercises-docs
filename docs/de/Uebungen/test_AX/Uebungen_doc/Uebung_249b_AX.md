# Uebung_249b_AX: Asynchrone Objektattribut-Abfrage mit gebündelter SubApp I_GetAttribute_IDA

![Uebung_249b_AX_network](./Uebung_249b_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung fragt bei einem Tasterdruck (`Input_I1`) ein Objektattribut eines VT-Objekts asynchron ab und zeigt das Ergebnis auf `OutputNumber_N1` an. Kommando (`GetAttribute`) und asynchroner Empfang der Antwort (`Attribute_IDA`) sind dabei in der gebündelten SubApp `MyLib::sys::I_GetAttribute_IDA` zusammengefasst; sie teilen sich die Parameter `u16ObjId` und `u8AID`.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**: logiBUS Eingang mit Klick-Erkennung
    - **Typ**: `logiBUS::io::DI::logiBUS_IE`
    - **Parameter**: `QI = TRUE`, `Input = Input_I1`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Meldet einen Einzelklick auf `I1` als Event `IND`.
- **I_GetAttribute_IDA_I1**: gebündelte Attributabfrage (SubApp-Instanz)
    - **Typ**: `MyLib::sys::I_GetAttribute_IDA`
    - **Parameter**: `u16ObjId = InputNumber_I1`, `u8AID = VALUE`
    - **Erklärung**: Bei `REQ` sendet der interne Baustein `I_GetAttribute` das Kommando GetAttribute (F.58) für das Attribut `u8AID` des Objekts `u16ObjId`; der interne Baustein `Attribute_IDA` empfängt die Antwort asynchron und stellt sie als `AD`-Adapter (DWORD) am Ausgang `IN` bereit. Hier wird der Wert (`VALUE`) des Eingabefelds `InputNumber_I1` abgefragt.
- **AD_TO_AR_NUM**: Adapterkonverter DWORD → REAL
    - **Typ**: `adapter::conversion::unidirectional::AD_TO_AR_NUM`
    - **Parameter**: keine
    - **Erklärung**: Wandelt den `AD`-Adapter numerisch (über UDINT, keine Bit-Umdeutung) in einen `AR`-Adapter (REAL) um.
- **OutputNumber_N1**: Ausgabe auf das VT-Zahlenfeld
    - **Typ**: `isobus::UT::Q::Q_NumericValue_PHYSA`
    - **Parameter**: `stObj = OutputNumber_N1_N`
    - **Erklärung**: Schreibt den physikalischen REAL-Wert (`rPhys`) in das VT-Objekt aus `stObj` (Ausgabefeld `OutputNumber_N1`).

## Programmablauf und Verbindungen

1. `DigitalInput_I1.IND` → `I_GetAttribute_IDA_I1.REQ`: der Tasterklick löst die Attributabfrage aus.
2. `I_GetAttribute_IDA_I1.IN` → `AD_TO_AR_NUM.AD_IN`: die asynchron eintreffende Antwort wird als DWORD-Adapter weitergegeben und numerisch in REAL gewandelt.
3. `AD_TO_AR_NUM.AR_OUT` → `OutputNumber_N1.rPhys`: der Wert wird auf `OutputNumber_N1` angezeigt.

**Verhalten**: Jeder Klick auf `I1` fragt den aktuellen Wert des Eingabefelds `InputNumber_I1` vom Terminal ab; sobald die Antwort eintrifft, erscheint er auf `OutputNumber_N1`.

## Zusammenfassung

Die Übung zeigt, wie sich Kommando und asynchroner Empfang einer Attributabfrage in einer einzigen SubApp bündeln lassen, sodass im Netzwerk nur noch ein Baustein mit gemeinsamer Objekt- und Attribut-ID nötig ist, und wie das Ergebnis über einen Adapterkonverter auf einem VT-Zahlenfeld ausgegeben wird.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
