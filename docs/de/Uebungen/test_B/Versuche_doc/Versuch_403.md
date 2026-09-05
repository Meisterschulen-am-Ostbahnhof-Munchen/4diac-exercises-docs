# Versuch_403: Array kopieren und erneut auswerten

![Versuch_403_network](./Versuch_403_network.svg)

* * * * * * * * * *

## Einleitung

Erweitert `Versuch_402` um eine Array-zu-Array-Kopie (`ARRAY2ARRAY_8_INT`): das erzeugte Array wird in eine zweite, unabhängige Array-Instanz kopiert, deren Elementanzahl separat erneut ermittelt wird.

## Verwendete Funktionsbausteine (FBs)

- **INIT** – Typ: `iec61131::booleanOperators::INIT` – löst die Kette selbst aus (siehe `Versuch_402`).
- **VALUES2ARRAY_8_INT** – Typ: `eclipse4diac::convert::VALUES2ARRAY_8_INT`
    - Parameter: `IN_1..IN_8 = 1, 22, 333, 4444, 333, 22, 1, 0`
- **GET_AT_INDEX** – Typ: `eclipse4diac::convert::GET_AT_INDEX`, Parameter: `INDEX = 0`
- **F_MOVE** – Typ: `iec61131::selection::F_MOVE`, Attribut: `DataType = INT`
- **CountOfElements** – Typ: `eclipse4diac::utils::arrays::F_LEN_ARRAY` – Länge des Original-Arrays.
- **F_UPPER_BOUND** / **F_LOWER_BOUND** – Typ: `iec61131::arrays::F_UPPER_BOUND`/`F_LOWER_BOUND`, jeweils Parameter `DIM = INT#1`.
- **ARRAY2ARRAY_8_INT** – Typ: `eclipse4diac::convert::ARRAY2ARRAY_8_INT`
    - Funktionsweise: kopiert ein 8‑elementiges Array in eine neue, unabhängige Array-Instanz.
- **CountOfElements_1** – Typ: `eclipse4diac::utils::arrays::F_LEN_ARRAY` – Länge der Array-Kopie (zweite, unabhängige Instanz).

## Programmablauf und Verbindungen

Wie in `Versuch_402` erzeugt `VALUES2ARRAY_8_INT.CNF` das Array und löst `GET_AT_INDEX`, `CountOfElements`, `F_LOWER_BOUND`, `F_UPPER_BOUND` sowie zusätzlich `ARRAY2ARRAY_8_INT.REQ` aus. `ARRAY2ARRAY_8_INT` kopiert das Array auf seinen eigenen `OUT`-Ausgang; dessen `CNF` löst wiederum `CountOfElements_1.REQ` aus, das die Länge der Kopie unabhängig vom Original ermittelt.

## Zusammenfassung

Zeigt, wie ein Array über `ARRAY2ARRAY_8_INT` als unabhängige Kopie weiterverarbeitet werden kann, ohne das Original zu verändern - beide Instanzen (Original und Kopie) werden hier separat auf ihre Länge geprüft.
