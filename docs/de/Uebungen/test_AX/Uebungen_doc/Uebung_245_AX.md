# Uebung_245_AX: Sollwert mit festem Korrekturfaktor skalieren (AR_MUL_2 + initval_AR) - Lastteiler-Kompensation, I3 nach N3

Dieser Artikel beschreibt die 4diac IDE Subapplikation Uebung_245_AX (Sollwert mit festem Korrekturfaktor skalieren (AR_MUL_2 + initval_AR) - Lastteiler-Kompensation, I3 nach N3).

----

![Uebung_245_AX_network](./Uebung_245_AX_network.svg)

## Ziel der Übung

Das Ziel dieser Übung ist die Umsetzung folgender Anforderung: **Sollwert mit festem Korrekturfaktor skalieren (AR_MUL_2 + initval_AR) - Lastteiler-Kompensation, I3 nach N3**

-----

## Beschreibung und Komponenten

Die Übung besteht aus der Subapplikation Uebung_245_AX.SUB, welche die folgende Bausteinstruktur verwendet:

### Verwendete Funktionsbausteine (FBs)

- **NumericValue_PHYSA**: Instanz des Typs isobus::UT::io::NumericValue::NumericValue_PHYSA.
  - Parameter QI = TRUE
  - Parameter stObj = InputNumber_I3_N
- **F_MUL_KORREKTUR**: Instanz des Typs adapter::iec61131::arithmetic::AR_MUL_2.
- **Q_NumericValue_PHYSA**: Instanz des Typs isobus::UT::Q::Q_NumericValue_PHYSA.
  - Parameter stObj = OutputNumber_N3_N
- **initval_AR**: Instanz des Typs adapter::types::unidirectional::AR::initval::initval_AR.
  - Parameter INIT_VAL = REAL#1.17619

### Verbindungen und Schnittstellen

**Adapterverbindungen:**
- F_MUL_KORREKTUR.OUT -> Q_NumericValue_PHYSA.rPhys
- initval_AR.OUT -> F_MUL_KORREKTUR.IN2
- NumericValue_PHYSA.rPhys -> F_MUL_KORREKTUR.IN1

### Hinweise aus dem Modell

> Korrekturfaktor 1,17619 = 1 / 0,8502: kompensiert einen Lastteiler (z.B. PWM-Tiefpassfilter, dessen Ausgangsimpedanz gegen die Eingangsimpedanz des angeschlossenen Verbrauchers belastet wird - siehe PVEA-Kompensationsrechnung). I3 = unkorrigierter Sollwert 0-100%, N3 = korrigierter Wert, der am unbelasteten Ausgang eingestellt werden muss, damit am Verbraucher der ursprüngliche Sollwert ankommt.

-----

## Programmablauf und Funktionsweise

1. **Initialisierung**: Beim Systemstart werden alle beteiligten Bausteine initialisiert.
2. **Ereignis- und Signalverarbeitung**: Zustandsänderungen an den Eingängen erzeugen Ereignisse, die über die definierten Verbindungen an die nachgelagerten Bausteine weitergeleitet werden.
3. **Ausgangsaktualisierung**: Die Zielbausteine verarbeiten die empfangenen Signale und aktualisieren entsprechend die Ausgänge.

-----

## Zusammenfassung

Die Übung Uebung_245_AX demonstriert anschaulich die modulare IEC 61499 Architektur in 4diac IDE.
