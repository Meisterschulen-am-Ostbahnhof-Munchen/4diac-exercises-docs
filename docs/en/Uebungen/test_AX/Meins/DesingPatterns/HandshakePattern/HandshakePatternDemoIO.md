# HandshakePatternDemoIO: Handshake Pattern on Physical I/O

![HandshakePatternDemoIO_network](./HandshakePatternDemoIO_network.svg)

* * * * * * * * * *

## Introduction

`HandshakePatternDemoIO` wires the handshake demo `HandshakePatternDemo` (IEC 61499 primer course, Module 6, Valeriy Vyatkin) to physical I/O: a button (`logiBUS_IE`) triggers the Requester, an LED (`logiBUS_QX`) toggles once the handshake round trip (REQ → CNF) has completed. According to the version note, the button/LED wiring style is taken from the example `test_B/Uebungen/Uebung_004a`. The SubApp interface is empty.

## Function Blocks (FBs) Used

| Instance | Type | Parameters | Explanation |
|---|---|---|---|
| `DigitalInput_CLK_I1` | [logiBUS_IE](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DI/logiBUS_IE/) | `QI` = `TRUE`, `Input` = `Input_I1`, `InputEvent` = `BUTTON_SINGLE_CLICK` | Button (single click) |
| `HandshakeDemo` | `Meins::DesingPatterns::HandshakePattern::HandshakePatternDemo` (SubApp) | `QI` = `TRUE` | Handshake demo with Requester and Responder; input `START`, output `DONE` |
| `E_T_FF` | [E_T_FF](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/StandardLibraries/events/E_T_FF/) | – | Toggle flip-flop: changes state on every `CLK` |
| `DigitalOutput_Q1` | [logiBUS_QX](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/logiBUS/io/DO/logiBUS_QX/) | `QI` = `TRUE`, `Output` = `Output_Q1` | LED on output Q1 |

## Program Flow and Connections

1. **Start**: `DigitalInput_CLK_I1.IND` → `HandshakeDemo.START` - the button press makes the Requester send its `REQ`.
2. **Feedback**: `HandshakeDemo.DONE` → `E_T_FF.CLK` - the LED is toggled only once the Responder's `CNF` has actually come back.
3. **LED**: `E_T_FF.EO` → `DigitalOutput_Q1.REQ`, `E_T_FF.Q` → `DigitalOutput_Q1.OUT`.
4. **Initialization**: `DigitalInput_CLK_I1.INITO` → `HandshakeDemo.INIT`, then `HandshakeDemo.INITO` → `DigitalOutput_Q1.INIT`.

## Summary

`HandshakePatternDemoIO` makes the handshake visible: each button press toggles the LED only after the request/confirm round trip has completed.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
