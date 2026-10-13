# ChainOfActionsDemo: Chain-of-Actions-Muster mit vier TrueUntil

![ChainOfActionsDemo_network](./ChainOfActionsDemo_network.svg)

* * * * * * * * * *

## Einleitung

`ChainOfActionsDemo` zeigt das Chain-of-Actions-Muster: vier Instanzen desselben Bausteins `TrueUntil` werden über `DONE` → `TRIGGER` hintereinander gekettet, statt einen großen Ablaufautomaten (ECC) zu verwenden (Kommentar im Typ). Schnittstelle: Ereigniseingang `START`, Ereignisausgang `DONE` sowie vier BOOL-Eingänge `Step1_InPosition`…`Step4_InPosition`, mit denen im Demo die Rückmeldung "Position erreicht" der jeweiligen Aktorik simuliert wird.

## Verwendete Funktionsbausteine (FBs)

| Instanz | Typ | Parameter | Erklärung |
|---|---|---|---|
| `Step1` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | - | Schritt 1 der Kette. |
| `Step2` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | - | Schritt 2 der Kette. |
| `Step3` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | - | Schritt 3 der Kette. |
| `Step4` | `Meins::DesingPatterns::ChainOfActionsPattern::TrueUntil` | - | Schritt 4 der Kette. |

## Programmablauf und Verbindungen

`TrueUntil` ist ein Basis-FB (im Projekt unter `DesingPatterns/ChainOfActionsPattern/TrueUntil.fbt`). Laut seiner ECC wartet er in `START` auf `TRIGGER` (oder `REQ`), gibt im Zustand `MOVING` das Ereignis `TO_POSITION` aus und wartet auf `inPosition`; danach gibt er `STOP` und `DONE` aus.

**Ereignisverbindungen:**

1. `START` → `Step1.TRIGGER`
2. `Step1.DONE` → `Step2.TRIGGER`
3. `Step2.DONE` → `Step3.TRIGGER`
4. `Step3.DONE` → `Step4.TRIGGER`
5. `Step4.DONE` → `DONE`

**Datenverbindungen:** `Step1_InPosition` → `Step1.inPosition`, entsprechend `Step2_InPosition` bis `Step4_InPosition` zu den jeweiligen Schritten.

Die Ausgänge `TO_POSITION` und `STOP` der Schritte sind in diesem Demo nicht verschaltet; sie wären mit der konkreten Aktorik zu verbinden.

## Zusammenfassung

Das Demo setzt eine Bewegungsfolge aus vier identischen, wiederverwendbaren Schritt-Bausteinen zusammen, die nur über `DONE` → `TRIGGER` verkettet sind.

---

### 🌐 Passende Themen-Unterseiten auf ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Farb-Referenz auf ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
