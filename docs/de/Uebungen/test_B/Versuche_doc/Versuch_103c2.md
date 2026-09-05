# Versuch_103c2: DigitalInput auf DigitalOutput über AX-Multiplexer-Adapter

![Versuch_103c2_network](./Versuch_103c2_network.svg)

* * * * * * * * * *

## Einleitung

Leitet einen digitalen Eingang über einen 3-fach-Multiplexer-Adapter (`AX_AUI_MUX_3`) auf einen digitalen Ausgang weiter - Plug-and-Socket-Adapterverbindung statt direkter Datenverbindung. Das dazugehörige `Uebung_103c2_AX` (test_AX) zeigt dasselbe Muster in der AX-Übungsumgebung; dieser Versuch ist die entsprechende Variante in `test_B`.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1** – Typ: `logiBUS::io::DI::logiBUS_IXA`
    - Parameter: `QI = TRUE`, `Input = Input_I1`
    - Funktionsweise: digitaler Eingang, stellt seinen Zustand über den `IN`-Adapteranschluss bereit.
- **AX_MUX_3** – Typ: `adapter::selection::unidirectional::AX_AUI_MUX_3`
    - Funktionsweise: 3-fach-Multiplexer-Adapter (unidirektional) - wählt eines von bis zu drei Eingangssignalen auf einen gemeinsamen Ausgang. Hier ist nur der erste Eingang (`IN1`) beschaltet.
- **DigitalOutput_Q1** – Typ: `logiBUS::io::DQ::logiBUS_QXA`
    - Parameter: `QI = TRUE`, `Output = Output_Q1`
    - Funktionsweise: digitaler Ausgang, übernimmt seinen Zustand über den `OUT`-Adapteranschluss.

## Programmablauf und Verbindungen

Adapterverbindungen (keine klassischen Daten-/Ereignisverbindungen): `DigitalInput_I1.IN` → `AX_MUX_3.IN1`, `AX_MUX_3.OUT` → `DigitalOutput_Q1.OUT`. Der physische Eingang `Input_I1` wird über den Multiplexer-Adapter durchgereicht und schaltet den physischen Ausgang `Output_Q1`.

## Zusammenfassung

Zeigt die einfachste Beschaltung eines `AX_AUI_MUX_3`-Adapters mit nur einem aktiven Eingang - demonstriert die Plug-and-Socket-Adapterverbindung anstelle klassischer Datenverbindungen.
