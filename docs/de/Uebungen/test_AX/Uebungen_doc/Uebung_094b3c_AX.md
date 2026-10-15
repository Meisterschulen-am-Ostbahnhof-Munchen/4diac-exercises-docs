# Uebung_094b3c_AX: Optionsauswahl mit zwei nacheinander aktivierten Optionsgruppen (Daisy-Chain)

![Uebung_094b3c_AX_network](./Uebung_094b3c_AX_network.svg)

* * * * * * * * * *

## Einleitung

Die Übung entspricht [`Uebung_094b3_AX`](Uebung_094b3_AX.md), aber der Index `K = 2` aktiviert zwei Optionsgruppen NACHEINANDER statt nur einer: [`OptionalGroup_001a`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001a/) wird vom DEMUX aktiviert und reicht die Aktivierung über ihren Plug `AOption_OUT` an [`OptionalGroup_001b`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001b/) weiter. Der DEMUX bekommt seine Bestätigung erst, wenn beide Gruppen initialisiert sind. Die Übung zeigt, wie man über die Plug/Socket-Kette mehrere Gruppen sequenziell (nacheinander, nicht gleichzeitig) hochfahren kann, ohne das Busy-Gating des DEMUX zu verletzen.

## Verwendete Funktionsbausteine (FBs)

- **NumericValue_TO_AUI2_1**: liest das VT-Eingabefeld und stellt es als bidirektionalen AUI2-Plug bereit
    - **Typ**: [`MyLib::sys::NumericValue_TO_AUI2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX/sys/NumericValue_TO_AUI2/) (SubApp-Instanz)
    - **Parameter**: `u16ObjId = InputNumber_I1`
    - **Erklärung**: Liest das VT-Eingabefeld und reicht den Wert über `VALUEO` an den DEMUX weiter. Der Consumer meldet den übernommenen Wert über denselben Plug zurück, das Echo wird auf das VT-Feld zurückgeschrieben.
- **AOption_AUI_DEMUX_3**: Optionsauswahl mit drei Ausgängen
    - **Typ**: [`Option::selection::AOption_AUI_DEMUX_3`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/Option/selection/AOption_AUI_DEMUX_3/)
    - **Parameter**: keine
    - **Erklärung**: Aktiviert abhängig vom Index `K` genau einen der Ausgänge `OUT1`, `OUT2`, `OUT3`.
- **DummyGroup**: neutraler Platzhalter-Slot
    - **Typ**: [`MyLib_AX_Option::sys::DummyGroup`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/DummyGroup/) (SubApp-Instanz)
    - **Parameter**: keine
    - **Erklärung**: Ohne reale I/O und ohne Wirkung; Zustand "nichts ausgewählt".
- **OptionalGroup_001a**: erste Optionsgruppe mit Weiterreichung
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_001a`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001a/) (SubApp-Instanz)
    - **Parameter**: keine
    - **Erklärung**: Schaltet `Input_I1`/`Output_Q1` frei und reicht die Aktivierung über den Plug `AOption_OUT` weiter.
- **OptionalGroup_001b**: zweite Optionsgruppe (Ende der Kette)
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_001b`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_001b/) (SubApp-Instanz)
    - **Parameter**: keine
    - **Erklärung**: Baugleich zu `OptionalGroup_001a`, aber ohne Plug; reicht nichts weiter.
- **OptionalGroup_002**: weitere Optionsgruppe
    - **Typ**: [`MyLib_AX_Option::sys::OptionalGroup_002`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/MyLib_AX_Option/sys/OptionalGroup_002/) (SubApp-Instanz)
    - **Parameter**: keine
    - **Erklärung**: Gruppe mit `I2`/`Q2`; wird über den Options-Adapter aktiviert bzw. deaktiviert.

## Programmablauf und Verbindungen

1. `NumericValue_TO_AUI2_1.VALUEO` → `AOption_AUI_DEMUX_3.K`: Der Wert des Eingabefelds wählt den aktiven Ausgang.
2. `AOption_AUI_DEMUX_3.OUT1` → `DummyGroup.AOption_IN`, `OUT2` → `OptionalGroup_001a.AOption_IN`, `OUT3` → `OptionalGroup_002.AOption_IN`.
3. `OptionalGroup_001a.AOption_OUT` → `OptionalGroup_001b.AOption_IN`: Die Aktivierung wird an die zweite Gruppe weitergereicht.

**Verhalten**: 1 = nichts ausgewählt, 2 = `OptionalGroup_001a` und anschließend `OptionalGroup_001b`, 3 = `OptionalGroup_002`. Die Bestätigung an den DEMUX erfolgt erst, wenn die gesamte Kette (001a und 001b) initialisiert ist.

## Zusammenfassung

Gegenüber `Uebung_094b3_AX` ist `OptionalGroup_001` durch `OptionalGroup_001a` ersetzt und mit der neuen `OptionalGroup_001b` verkettet. So werden mehrere Gruppen nacheinander aktiviert, bevor der Aufrufer seine Bestätigung erhält.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
