# Uebung_OPC_L14_AX: Adder lokal: SUBSCRIBE Num1/Num2, F_ADD, PUBLISH Result

![Uebung_OPC_L14_AX_network](./Uebung_OPC_L14_AX_network.svg)

* * * * * * * * * *

## Einleitung

`Uebung_OPC_L14_AX` ist das Adder-Beispiel aus `opcua.adoc`, vollständig lokal auf einem Gerät: Zwei OPC-UA-Variablen (Num1, Num2) werden per `SUBSCRIBE_1` gelesen, mit `F_ADD` addiert und das Ergebnis per `PUBLISH_1` auf die Variable Result geschrieben. Die Werte für Num1 und Num2 setzt man z. B. mit UaExpert. Alle drei Zugriffe laufen ohne Endpunkt (lokal).

Übersicht der Serie: [`OPC_UA_Lokal.md`](./OPC_UA_Lokal.md).

Kommentar im `.SUB`: „Adder (opcua.adoc): SUBSCRIBE Num1/Num2 -> F_ADD -> PUBLISH Result, Werte z.B. per UaExpert - lokal auf einem Geraet“

## Verwendete Funktionsbausteine (FBs)

### `NUM1`

- Typ: `iec61499::net::SUBSCRIBE_1`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L14_NUM1_READ`

- Erklärung: Abonniert lokal (ohne Endpunkt) den Knoten Num1; der Wert (INT) kommt an `RD_1`, jede Änderung löst `IND` aus. Der Wert wird z. B. per UaExpert gesetzt.

### `NUM2`

- Typ: `iec61499::net::SUBSCRIBE_1`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L14_NUM2_READ`

- Erklärung: Wie `NUM1`, aber für den Knoten Num2.

### `TO_DINT_1`

- Typ: `iec61131::conversion::F_INT_TO_DINT`
- Parameter: keine gesetzt

- Erklärung: Wandelt den INT-Wert von `NUM1` in DINT, weil `F_ADD` mit DINT rechnet.

### `TO_DINT_2`

- Typ: `iec61131::conversion::F_INT_TO_DINT`
- Parameter: keine gesetzt

- Erklärung: Wie `TO_DINT_1`, für `NUM2`.

### `F_ADD`

- Typ: `iec61131::arithmetic::F_ADD`
- Parameter: keine gesetzt

- Erklärung: Addiert `IN1` und `IN2` zu `OUT` (Num1 + Num2). Beide Wandler lösen denselben Eingang `REQ` aus.

### `TO_INT`

- Typ: `iec61131::conversion::F_DINT_TO_INT`
- Parameter: keine gesetzt

- Erklärung: Wandelt die DINT-Summe zurück in INT für den Knoten Result.

### `RESULT`

- Typ: `iec61499::net::PUBLISH_1`
- Parameter:

    - `QI` = `TRUE`
    - `ID` = `L14_RESULT_WRITE`

- Erklärung: Schreibt lokal (ohne Endpunkt) die Summe auf den Knoten Result (`SD_1`); er ist per UaExpert lesbar.

## OPC-UA-Adressen (`OpcUaLokal.gcf`)

Die IDs stammen aus `const/OpcUaLokal.gcf` (Paket `Meins::OPC_UA_Lokal::const`).

| Konstante | Aktion | Knoten / ID |
|---|---|---|
| `L14_NUM1_READ` | READ | `/Objects/Lokal/L14/Num1,1:s=L14_Num1` |
| `L14_NUM2_READ` | READ | `/Objects/Lokal/L14/Num2,1:s=L14_Num2` |
| `L14_RESULT_WRITE` | WRITE | `/Objects/Lokal/L14/Result,1:s=L14_Result` |

## Programmablauf und Verbindungen

### Ereignisverbindungen

1. `NUM1.IND` → `TO_DINT_1.REQ`
2. `NUM2.IND` → `TO_DINT_2.REQ`
3. `TO_DINT_1.CNF` → `F_ADD.REQ`
4. `TO_DINT_2.CNF` → `F_ADD.REQ`
5. `F_ADD.CNF` → `TO_INT.REQ`
6. `TO_INT.CNF` → `RESULT.REQ`
7. `NUM1.INITO` → `NUM2.INIT`
8. `NUM2.INITO` → `RESULT.INIT`

### Datenverbindungen

1. `NUM1.RD_1` → `TO_DINT_1.IN`
2. `NUM2.RD_1` → `TO_DINT_2.IN`
3. `TO_DINT_1.OUT` → `F_ADD.IN1`
4. `TO_DINT_2.OUT` → `F_ADD.IN2`
5. `F_ADD.OUT` → `TO_INT.IN`
6. `TO_INT.OUT` → `RESULT.SD_1`

## Hinweis / Ergebnis

Nicht auf dem Gerät getestet. Erwartet: Result = Num1 + Num2, sobald Num1 und Num2 per UaExpert gesetzt sind.

Dasselbe Beispiel auf zwei Geräten: [Training_14_OPC_UA_RES](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-exercises-docs/de/latest/Uebungen/test_VV/03_OPC_UA/Training_14_OPC_UA_RES/Training_14_OPC_UA_RES/).

## Lernziele

- Lokales Lesen (`SUBSCRIBE_1`) und Schreiben (`PUBLISH_1`) von OPC-UA-Variablen ohne Endpunkt
- Typanpassung INT ↔ DINT mit `F_INT_TO_DINT` / `F_DINT_TO_INT` vor und nach `F_ADD`
- Initialisierungskette: `INITO` eines Bausteins startet `INIT` des nächsten

## Zusammenfassung

Die Übung zeigt eine kleine Berechnung (Addition) zwischen lokal abonnierten und lokal veröffentlichten OPC-UA-Variablen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
