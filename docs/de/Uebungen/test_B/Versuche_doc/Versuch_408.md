# Versuch_408: Array-Statistik in sequenzieller Verkettung

![Versuch_408_network](./Versuch_408_network.svg)

* * * * * * * * * *

## Einleitung

Berechnet dieselbe Array-Statistik wie `Versuch_406` (Max, Summe, Min, Indexzugriff), aber - anders als dort - nicht parallel, sondern **sequenziell verkettet**: jeder Baustein löst erst den nächsten aus, wenn seine eigene Berechnung fertig ist (`CNF`). Zusätzlich wird das Array selbst über die `A`/`IN_ARRAY`-Datenanschlüsse von Baustein zu Baustein durchgereicht, statt allen Konsumenten gleichzeitig aus derselben Quelle zuzuführen.

## Verwendete Funktionsbausteine (FBs)

- **INIT_ARR_8_INT** – Typ: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [0, 1, 22, 333, 4444, 333, 22, 1]`
- **ARR_MAX** – Typ: `logiBUS::utils::dyn_arr::ARR_MAX`
- **SUM** – Typ: `logiBUS::utils::dyn_arr::SUM`
- **ARR_MIN** – Typ: `logiBUS::utils::dyn_arr::ARR_MIN`
- **GET_AT_INDEX** – Typ: `eclipse4diac::convert::GET_AT_INDEX`, Parameter: `INDEX = 4` (Element `4444`)

## Programmablauf und Verbindungen

Ereigniskette: `INIT_ARR_8_INT.INITO` → `ARR_MAX.REQ` → (nach `ARR_MAX.CNF`) `SUM.REQ` → (nach `SUM.CNF`) `ARR_MIN.REQ` → (nach `ARR_MIN.CNF`) `GET_AT_INDEX.REQ`. Parallel dazu wird das Array selbst datentechnisch weitergereicht: `INIT_ARR_8_INT.D1` → `ARR_MAX.A` → `SUM.A` → `ARR_MIN.A` → `GET_AT_INDEX.IN_ARRAY` - jeder Baustein reicht sein eigenes `A`-Array unverändert an den nächsten weiter, statt dass alle direkt aus derselben Quelle lesen.

## Zusammenfassung

Zeigt die sequenzielle (statt parallele) Verkettung von Array-Auswertungen samt Durchreichen des Array-Datenwerts von Baustein zu Baustein - ein alternatives Verdrahtungsmuster zu den parallelen Varianten `Versuch_401`/`Versuch_406`.
