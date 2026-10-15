# Uebung_152_AX: Impulszähler und PI-Regler mit Plug and Socket (adapternativ)

![Uebung_152_AX_network](./Uebung_152_AX_network.svg)

* * * * * * * * * *

## Einleitung

Diese Übung misst Impulse an `I1`, bildet daraus die Ableitung (`FT_DERIV_10_AR`), regelt diese mit einem PI-Regler (`CTRL_PI_AR`) auf einen Sollwert und gibt den Stellwert als PWM-Signal an `Q1` aus. Die gesamte Signalkette ist adapternativ aufgebaut: Istwert, Sollwert, Handumschaltung und alle Reglerparameter werden über Adapter-Sockets zugeführt, die festen Werte kommen aus `initval_AR`-/`initval_AUI`-Konstantenbausteinen.

## Verwendete Funktionsbausteine (FBs)

- **Input_I2**: Klick-Eingang: Messung starten
    - **Typ**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I2`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Meldet einen Einzelklick auf `I2` als Event `IND`.
- **Input_I3**: Klick-Eingang: Messung stoppen
    - **Typ**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I3`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Meldet einen Einzelklick auf `I3` als Event `IND`.
- **E_CYCLE**: Abtasttakt
    - **Typ**: `iec61499::events::E_CYCLE`
    - **Parameter**: `DT = T#200ms`
    - **Erklärung**: Erzeugt nach `START` alle 200 ms ein Event `EO`, bis `STOP` eintrifft.
- **Input_I1**: Impulseingang
    - **Typ**: [`logiBUS::io::PI::logiBUS_PI_IDA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/PI/logiBUS_PI_IDA/)
    - **Parameter**: `QI = TRUE`, `Input = PulseInput_I1`, `ImpulseDelta = 100`, `TimeDelta = 50000`, `TimeRateLimit = 10000`
    - **Erklärung**: Zählt die Impulse an `I1` und gibt sie bei jeder Anfrage (`REQ`) über den Adapter `IN` aus.
- **AD_TO_AR_NUM**: Adapter-Konvertierung
    - **Typ**: [`adapter::conversion::unidirectional::AD_TO_AR_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AD_TO_AR_NUM/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt den `AD`-Adapterwert in einen `AR`-Adapterwert (REAL) um.
- **LIT_K**: Konstante für das Fenster der Ableitung
    - **Typ**: [`adapter::types::unidirectional::AUI::initval::initval_AUI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/UINT/initval/initval_AUI/)
    - **Parameter**: `INIT_VAL = 9`
    - **Erklärung**: Liefert die Konstante `9` als `AUI`-Adapterwert an `FT_DERIV.K`.
- **FT_DERIV**: Ableitung (Drehzahl aus Impulsen)
    - **Typ**: `OSCAT_adapter::Control::FT_DERIV_10_AR`
    - **Parameter**: `Faktor = 1.0`, `run = TRUE`
    - **Erklärung**: AR-Adapter-Wrapper um OSCAT `FT_DERIV_10`: bildet aus dem Eingangssignal (`AR_IN`) die Ableitung. Das Fenster wird über den Adapter `K` gewählt (`0` = 1 Periode, `9` = 10 Perioden).
- **Input_I4**: Klick-Eingang: Hand/Automatik
    - **Typ**: [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/)
    - **Parameter**: `QI = TRUE`, `Input = Input_I4`, `InputEvent = BUTTON_SINGLE_CLICK`
    - **Erklärung**: Meldet einen Einzelklick auf `I4` als Event `IND`.
- **AX_T_FF**: Toggle-Flip-Flop
    - **Typ**: [`adapter::events::unidirectional::AX_T_FF`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/AX_T_FF/)
    - **Parameter**: keine
    - **Erklärung**: Wechselt mit jedem `CLK`-Event den Zustand von `Q`.
- **initval_AR**: Sollwert
    - **Typ**: [`adapter::types::unidirectional::AR::initval::initval_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/REAL/initval/initval_AR/)
    - **Parameter**: `INIT_VAL = 16.0`
    - **Erklärung**: Liefert den Sollwert `16.0` an `CTRL_PI.SET`.
- **LIT_SUP, LIT_OFS, LIT_M_I, LIT_KP, LIT_KI, LIT_LL, LIT_LH**: Konstanten für die Reglerparameter
    - **Typ**: [`adapter::types::unidirectional::AR::initval::initval_AR`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/REAL/initval/initval_AR/)
    - **Parameter**: `INIT_VAL` = `0.0` (`LIT_SUP`), `0.0` (`LIT_OFS`), `8184.0` (`LIT_M_I`), `10.0` (`LIT_KP`), `10.0` (`LIT_KI`), `0.0` (`LIT_LL`), `8184.0` (`LIT_LH`)
    - **Erklärung**: Liefern als `AR`-Adapterwerte: Rauschunterdrückung `SUP`, Offset `OFS`, Handwert `M_I`, Proportionalbeiwert `KP`, Integralbeiwert `KI`, unteren Grenzwert `LL` und oberen Grenzwert `LH` an `CTRL_PI`.
- **CTRL_PI**: PI-Regler
    - **Typ**: `OSCAT_adapter::Control::CTRL_PI_AR`
    - **Parameter**: keine
    - **Erklärung**: AR-Adapter-Wrapper um OSCAT `CTRL_PI`: Istwert (`AR_ACT`), Sollwert (`SET`), Handumschaltung (`AX_MAN`) und alle Reglerparameter sind Adapter-Sockets; der Stellwert liegt am Adapter `AR_Y`.
- **AR_TO_AD_NUM**: Adapter-Konvertierung
    - **Typ**: [`adapter::conversion::unidirectional::AR_TO_AD_NUM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/adapter/conversion/unidirectional/AD_AR/AR_TO_AD_NUM/)
    - **Parameter**: keine
    - **Erklärung**: Wandelt den `AR`-Stellwert in einen `AD`-Adapterwert um.
- **PWMOutput_Q1**: PWM-Ausgang
    - **Typ**: [`logiBUS::io::DQ::logiBUS_QDA_PWM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/de/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QDA_PWM/)
    - **Parameter**: `QI = TRUE`, `Output = Output_Q1`
    - **Erklärung**: Gibt den Stellwert als PWM-Signal am physischen Ausgang `Q1` aus.

## Programmablauf und Verbindungen

1. `Input_I2.IND` → `E_CYCLE.START` und `Input_I3.IND` → `E_CYCLE.STOP` (EventConnections): `I2` startet, `I3` stoppt den 200-ms-Takt.
2. `E_CYCLE.EO` → `Input_I1.REQ` (EventConnection): Mit jedem Takt wird der Impulseingang abgefragt.
3. `Input_I1.IN` → `AD_TO_AR_NUM.AD_IN` und `AD_TO_AR_NUM.AR_OUT` → `FT_DERIV.AR_IN` (AdapterConnections): Der Impulswert wird in einen `AR`-Wert gewandelt und der Ableitung zugeführt. `LIT_K.OUT` → `FT_DERIV.K` legt das Fenster fest.
4. `FT_DERIV.AR_OUT` → `CTRL_PI.AR_ACT` (AdapterConnection): Die Ableitung ist der Istwert des Reglers; `initval_AR.OUT` → `CTRL_PI.SET` liefert den Sollwert `16.0`.
5. `LIT_SUP.OUT`, `LIT_OFS.OUT`, `LIT_M_I.OUT`, `LIT_KP.OUT`, `LIT_KI.OUT`, `LIT_LL.OUT`, `LIT_LH.OUT` → `CTRL_PI.SUP`/`OFS`/`M_I`/`KP`/`KI`/`LL`/`LH` (AdapterConnections): Die Reglerparameter kommen als Konstanten-Adapter.
6. `Input_I4.IND` → `AX_T_FF.CLK` (EventConnection) und `AX_T_FF.Q` → `CTRL_PI.AX_MAN` (AdapterConnection): Jeder Klick auf `I4` schaltet die Handumschaltung des Reglers um.
7. `CTRL_PI.AR_Y` → `AR_TO_AD_NUM.AR_IN` und `AR_TO_AD_NUM.AD_OUT` → `PWMOutput_Q1.OUT` (AdapterConnections): Der Stellwert wird gewandelt und als PWM an `Q1` ausgegeben.

**Verhalten**: Nach einem Klick auf `I2` wird alle 200 ms der Impulseingang abgefragt, bis `I3` den Takt stoppt. Der Regler arbeitet auf den Sollwert `16.0`; `I4` schaltet die Handumschaltung des Reglers (`AX_MAN`) um. Der Stellwert ist auf `LL = 0.0` bis `LH = 8184.0` begrenzt.

## Zusammenfassung

Die Übung zeigt einen Regelkreis, der vollständig mit Plug und Socket (Adaptern) verdrahtet ist. Seit Version 1.3 sind die früheren Parameter an `CTRL_PI` und `FT_DERIV` durch Konstanten-Bausteine (`initval_AR`/`initval_AUI`) und Adapterverbindungen ersetzt, weil `CTRL_PI_AR` und `FT_DERIV_10_AR` diese Eingänge nur noch als Adapter-Sockets besitzen.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
