# Versuch_400: Array-Konstante bereitstellen

![Versuch_400_network](./Versuch_400_network.svg)

* * * * * * * * * *

## Einleitung

Der einfachste Baustein der Array-Versuchsreihe: ein fest verdrahtetes 8‑elementiges INT‑Array wird bereitgestellt, aber (noch) von keinem weiteren Baustein verwendet. Dient als Ausgangspunkt für die folgenden Versuche (`Versuch_401`ff.), die auf dieser Array-Bereitstellung aufbauen.

## Verwendete Funktionsbausteine (FBs)

- **INIT_ARR_8_INT** – Typ: `eclipse4diac::convert::providers::PROVIDE_ARR_0008_INT`
    - Parameter: `D1 = [0, 1, 22, 333, 4444, 333, 22, 1]`
    - Funktionsweise: stellt bei jedem Aufruf (bzw. beim FORTE-Start über sein internes `INIT`-Ereignis) das feste 8‑elementige Array als Datenausgang `D1` bereit und feuert `INITO`.

## Programmablauf und Verbindungen

Es gibt nur einen Baustein und keine Verbindungen zu anderen Bausteinen - `INIT_ARR_8_INT` initialisiert sich beim Start selbst und hält seinen Array-Wert konstant am Ausgang `D1` bereit.

## Zusammenfassung

Zeigt die reine Bereitstellung einer Array-Konstante über `PROVIDE_ARR_0008_INT`, ohne jede weitere Verarbeitung - Grundbaustein für die komplexeren Array-Versuche dieser Reihe.
