# Uebung_153_AX: Impulszähler & PID Regler, mit Plug and Socket (Adapter-nativ)

![Uebung_153_AX_network](./Uebung_153_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung kombiniert einen Impulszähler (`logiBUS_PI_IDA`, Eingang `I3`) mit einem PID-Regler und gibt die Stellgröße als PWM an `Q1` aus. Der Datenfluss läuft vollständig über Adapter (Plug and Socket): `FT_DERIV_10_AR` und `CTRL_PID_AR` stammen aus `OSCAT_adapter`, `AD_TO_AR_NUM` und `AR_TO_AD_NUM` wandeln zwischen den Adaptertypen. Die Reglerparameter (`SET`, `SUP`, `OFS`, `M_I`, `KP`, `TN`, `TV`, `LL`, `LH`) und der Faktor `K` des Differenzierers werden nicht mehr als Parameter, sondern über Literal-Bausteine (`initval_AR`/`initval_AUI`) per Adapter zugeführt. Mit `I1` und `I2` wird die zyklische Abfrage gestartet und gestoppt, mit `I4` wird der Handbetrieb des Reglers umgeschaltet.

## Verwendete Funktionsbausteine (FBs)

- **DigitalInput_I1**, **DigitalInput_I2**, **DigitalInput_I4**: Taster mit Klick-Erkennung
    - **Typ**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I1` / `Input_I2` / `Input_I4`, jeweils `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Melden einen Einzelklick als Event `IND`.
- **E_CYCLE**: Abtasttakt
    - **Typ**: [`iec61499::events::E_CYCLE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/StandardLibraries/events/E_CYCLE/)
    - **Parameter**: `DT = T#200ms`
    - **Erklärung**: Wird durch `I1` gestartet (`START`), durch `I2` gestoppt (`STOP`) und fordert alle 200 ms einen neuen Impulswert an.
- **logiBUS_PI_IDA**: Impulseingang
    - **Typ**: [`logiBUS::io::PI::logiBUS_PI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/PI/logiBUS_PI_IDA/)
    - **Parameter**: `QI = TRUE`, `Input = PulseInput_I3`, `ImpulseDelta = 100`, `TimeDelta = 50000`, `TimeRateLimit = 10000`
    - **Erklärung**: Liefert den Impulswert über den Adapter `IN` bei jedem `REQ`.
- **AD_TO_AR_NUM**: Konvertierung `AD` nach `AR`
    - **Typ**: [`adapter::conversion::unidirectional::AD_TO_AR_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AD_TO_AR_NUM/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt den Impulswert (`AD_IN`) numerisch in einen `REAL`-Adapterwert (`AR_OUT`).
- **LIT_K**: Faktor `K` des Differenzierers
    - **Typ**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameter**: `INIT_VAL = 9`
    - **Erklärung**: Liefert `K = 9` an `FT_DERIV.K`.
- **FT_DERIV**: Differenzierer mit Verzögerung
    - **Typ**: `OSCAT_adapter::Control::FT_DERIV_10_AR`
    - **Parameter**: `Faktor = 1.0`, `run = TRUE`
    - **Erklärung**: Bildet aus dem `REAL`-Wert (`AR_IN`) die Änderungsrate (`AR_OUT`), die als Istwert an den Regler geht.
- **AX_T_FF**: Toggle-Flipflop
    - **Typ**: [`adapter::events::unidirectional::AX_T_FF`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_T_FF/)
    - **Parameter**: keine
    - **Erklärung**: Jeder Klick auf `I4` (`CLK`) kehrt `Q` um; `Q` geht an den Eingang `AX_MAN` des Reglers.
- **initval_AR**: Sollwert
    - **Typ**: [`adapter::types::unidirectional::AR::initval::initval_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/REAL/initval/initval_AR/)
    - **Parameter**: `INIT_VAL = 16.0`
    - **Erklärung**: Liefert den Sollwert an `CTRL_PID.SET`.
- **CTRL_PID**: PID-Regler
    - **Typ**: [`OSCAT_adapter::Control::CTRL_PID_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/OSCAT/Basic/POUs/Engineering/Control/CTRL_PID_AR/)
    - **Parameter**: keine (alle Eingänge sind Adapter)
    - **Erklärung**: Erhält Istwert (`AR_ACT`), Sollwert (`SET`), Handbetrieb (`AX_MAN`) und die Reglerparameter über Adapter; liefert die Stellgröße `AR_Y`.
- **LIT_SUP**, **LIT_OFS**, **LIT_M_I**, **LIT_KP**, **LIT_TN**, **LIT_TV**, **LIT_LL**, **LIT_LH**: Literal-Bausteine für die Reglerparameter
    - **Typ**: [`adapter::types::unidirectional::AR::initval::initval_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/REAL/initval/initval_AR/)
    - **Parameter**: `INIT_VAL` = `0.0` (`LIT_SUP`), `0.0` (`LIT_OFS`), `8184.0` (`LIT_M_I`), `10.0` (`LIT_KP`), `1.0` (`LIT_TN`), `1.0` (`LIT_TV`), `0.0` (`LIT_LL`), `8184.0` (`LIT_LH`)
    - **Erklärung**: Jeder Baustein speist den gleichnamigen Eingang (`SUP`, `OFS`, `M_I`, `KP`, `TN`, `TV`, `LL`, `LH`) von `CTRL_PID`.
- **AR_TO_AD_NUM**: Konvertierung `AR` nach `AD`
    - **Typ**: [`adapter::conversion::unidirectional::AR_TO_AD_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AR_TO_AD_NUM/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt die Stellgröße (`AR_IN`) in einen `AD`-Adapterwert (`AD_OUT`).
- **PWMOutput_Q1**: PWM-Ausgang
    - **Typ**: [`logiBUS::io::DQ::logiBUS_QDA_PWM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QDA_PWM/)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt den Wert von `AR_TO_AD_NUM.AD_OUT` als PWM an `Q1` aus.

## Programmablauf und Verbindungen

1. `DigitalInput_I1.IND` → `E_CYCLE.START` und `DigitalInput_I2.IND` → `E_CYCLE.STOP` (EventConnections): `I1` startet, `I2` stoppt den 200-ms-Takt.
2. `E_CYCLE.EO` → `logiBUS_PI_IDA.REQ` (EventConnection): Bei jedem Takt wird der Impulswert angefordert.
3. `logiBUS_PI_IDA.IN` → `AD_TO_AR_NUM.AD_IN`, `AD_TO_AR_NUM.AR_OUT` → `FT_DERIV.AR_IN` und `LIT_K.OUT` → `FT_DERIV.K` (AdapterConnections): Der Impulswert wird gewandelt und differenziert.
4. `FT_DERIV.AR_OUT` → `CTRL_PID.AR_ACT` (AdapterConnection): Das Ergebnis ist der Istwert des Reglers.
5. `initval_AR.OUT` → `CTRL_PID.SET` (AdapterConnection): Sollwert 16.0.
6. `DigitalInput_I4.IND` → `AX_T_FF.CLK` und `AX_T_FF.Q` → `CTRL_PID.AX_MAN` (Event- und AdapterConnection): Jeder Klick auf `I4` schaltet den Handbetrieb des Reglers um.
7. `LIT_SUP.OUT` → `CTRL_PID.SUP`, `LIT_OFS.OUT` → `CTRL_PID.OFS`, `LIT_M_I.OUT` → `CTRL_PID.M_I`, `LIT_KP.OUT` → `CTRL_PID.KP`, `LIT_TN.OUT` → `CTRL_PID.TN`, `LIT_TV.OUT` → `CTRL_PID.TV`, `LIT_LL.OUT` → `CTRL_PID.LL`, `LIT_LH.OUT` → `CTRL_PID.LH` (AdapterConnections): Die Reglerparameter.
8. `CTRL_PID.AR_Y` → `AR_TO_AD_NUM.AR_IN` und `AR_TO_AD_NUM.AD_OUT` → `PWMOutput_Q1.OUT` (AdapterConnections): Die Stellgröße wird als PWM an `Q1` ausgegeben.

## Zusammenfassung

Die Übung 153_AX zeigt einen Regelkreis, bei dem Impulserfassung, Differenzierung und PID-Regler vollständig über Adapter verbunden sind. Reglerparameter und Differenzierfaktor werden dabei nicht mehr als Parameter gesetzt, sondern über `initval_AR`/`initval_AUI`-Literale mit gleichen Werten wie zuvor zugeführt.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
