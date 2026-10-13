# Maehwerksklappung_DM: Mower folding with deadman

![Maehwerksklappung_DM_network](./Maehwerksklappung_DM_network.svg)

* * * * * * * * * *

## Introduction

`Maehwerksklappung_DM` is the "mower folding" task from February 2026 (type comment) in the variant with a deadman switch (`DM`). An eight-step sequencer `sequence_E_08_AX_DM` switches the outputs `Output_Q1`...`Output_Q6` for raising, lowering, locking, unlocking, green lamp and orange flashing lamp, driven by six inputs (`Input_I1`...`Input_I6`). The SubApp has no interface of its own.

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `Arbeit` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I1`, `InputEvent` = `BUTTON_SINGLE_CLICK` | Work button: starts or ends the sequence. |
| `S_1B1_riegel_offen` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I2`, `InputEvent` = `BUTTON_PRESS_DOWN` | Latch-open signal. |
| `S_1B2_riegel_geschlossen` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I3`, `InputEvent` = `BUTTON_PRESS_DOWN` | Latch-closed signal. |
| `S_1B2_riegel_geschlossen_1` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I3`, `InputEvent` = `BUTTON_PRESS_UP` | Same input I3, but event on release. |
| `S_2B1_M_unten` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I4`, `InputEvent` = `BUTTON_PRESS_DOWN` | Mower-down signal. |
| `S_2B2_M_oben` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I5`, `InputEvent` = `BUTTON_PRESS_DOWN` | Mower-up signal. |
| `Transport` | [`logiBUS::io::DI::logiBUS_IE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `Input` = `Input_I6`, `InputEvent` = `BUTTON_SINGLE_CLICK` | Transport button. |
| `Arbeit_1` | [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input` = `Input_I1` | Level of I1 as adapter (for the deadman branch). |
| `Transport_1` | [`logiBUS::io::DI::logiBUS_IXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IXA/) | `Input` = `Input_I6` | Level of I6 as adapter (for the deadman branch). |
| `AX_OR_2` | [`adapter::booleanOperators::AX_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | - | Combines `Arbeit_1` and `Transport_1`. |
| `AX_TON` | [`adapter::events::unidirectional::timers::AX_TON`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/timers/AX_TON/) | `PT` = `T#1s` | On-delay of 1 s; output `Q` feeds the `DM` input of the sequence. |
| `sequence_E_08_AX` | [`logiBUS::utils::sequence::event::sequence_E_08_AX_DM`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/utils/sequence/event/sequence_E_08_AX_DM/) | - | Eight-step event-driven sequencer with deadman input (`DM`); outputs `DO_S1`...`DO_S8`. |
| `AX_OR_entriegeln` | [`adapter::booleanOperators::AX_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | - | `DO_S1` or `DO_S5` → unlock output. |
| `AX_OR_verriegeln` | [`adapter::booleanOperators::AX_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | - | `DO_S3` or `DO_S7` → lock output. |
| `AX_OR_Lampe_gruen` | [`adapter::booleanOperators::AX_OR_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/booleanOperators/AX_OR_2/) | - | `DO_S4` or `DO_S8` → green lamp. |
| `AX_BLINK` | [`adapter::events::unidirectional::signals::AX_BLINK`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/events/unidirectional/BOOL/signals/AX_BLINK/) | `TIMELOW` = `T#1s`, `TIMEHIGH` = `T#500ms` | Blink generator for the orange lamp. |
| `Ausgang_heben` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q1` | Raise the mower. |
| `Ausgang_senken` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q2` | Lower the mower. |
| `Ausgang_verriegeln` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q3` | Lock. |
| `Ausgang_entriegeln` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q4` | Unlock. |
| `Lampe_gruen` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q5` | Green lamp. |
| `Blinker_orange` | [`logiBUS::io::DQ::logiBUS_QXA`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QXA/) | `Output` = `Output_Q6` | Orange flashing lamp. |

## Program Flow and Connections

**Step chain (events → `sequence_E_08_AX`):**

| Transition | Triggering event |
|---|---|
| `START_S1` | `Arbeit.IND` |
| `S1_S2` | `S_1B1_riegel_offen.IND` |
| `S2_S3` | `S_2B1_M_unten.IND` |
| `S3_S4` | `S_1B2_riegel_geschlossen.IND` |
| `S4_S5` | `Transport.IND` |
| `S5_S6` | `S_1B1_riegel_offen.IND` |
| `S6_S7` | `S_2B2_M_oben.IND` |
| `S7_S8` | `S_1B2_riegel_geschlossen.IND` |
| `S8_START` | `Arbeit.IND` |

The comments in the network name S4 the "Arbeit" (work) state and S8 the "Transport" state.

**Mapping of the state outputs:**

1. `DO_S1` and `DO_S5` → `AX_OR_entriegeln` → `Ausgang_entriegeln` (Q4).
2. `DO_S2` → `Ausgang_senken` (Q2).
3. `DO_S3` and `DO_S7` → `AX_OR_verriegeln` → `Ausgang_verriegeln` (Q3).
4. `DO_S4` and `DO_S8` → `AX_OR_Lampe_gruen` → `Lampe_gruen` (Q5).
5. `DO_S6` → `Ausgang_heben` (Q1).

**Flasher:** `S_1B2_riegel_geschlossen.IND` (I3 pressed) → `AX_BLINK.STOP`; `S_1B2_riegel_geschlossen_1.IND` (I3 released) → `AX_BLINK.START`; `AX_BLINK.OUT` → `Blinker_orange` (Q6).

**Deadman:** `Arbeit_1.IN` and `Transport_1.IN` → `AX_OR_2` → `AX_TON` (1 s) → `sequence_E_08_AX.DM`.

## Summary

`Maehwerksklappung_DM` shows a fully event-driven eight-step sequence (unlock, lower, lock, work, unlock, raise, lock, transport) with deadman release via a 1 s on-delay, plus a flashing indicator.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
