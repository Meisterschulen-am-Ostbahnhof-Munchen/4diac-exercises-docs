# Uebung_000_AX: Simple Addition

![Uebung_000_AX_network](./Uebung_000_AX_network.svg)

* * * * * * * * * *

## Introduction

This exercise is the most basic example for calculations: two constant integer values are routed via adapter connections to an addition block of the adapter-based IEC 61131 library. It shows how a standard arithmetic block is placed in an IEC 61499 network and supplied with values. The exercise has no inputs or outputs on the SubApp interface.

## Function Blocks (FBs) Used

- **initval_AI**: Constant value source for the first summand
    - **Type**: [`adapter::types::unidirectional::AI::initval::initval_AI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/INT/initval/initval_AI/)
    - **Parameters**: `INIT_VAL = INT#5`
    - **Explanation**: Outputs the value 5 on initialization via the adapter output `OUT` (adapter `AI`).
- **initval_AI_1**: Constant value source for the second summand
    - **Type**: [`adapter::types::unidirectional::AI::initval::initval_AI`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/types/unidirectional/INT/initval/initval_AI/)
    - **Parameters**: `INIT_VAL = INT#3`
    - **Explanation**: Outputs the value 3 on initialization via the adapter output `OUT`.
- **ADD_2**: Addition block
    - **Type**: [`adapter::iec61131::arithmetic::AI_ADD_2`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/adapter/iec61131-3/arithmetic/AI_ADD_2/)
    - **Parameters**: none
    - **Explanation**: Adds the values supplied via the adapters `IN1` and `IN2` (each of type `AI`) and provides the sum on the adapter `OUT`. According to the type comment, the adapter output is only updated when the value changes.

## Program Flow and Connections

1. `initval_AI.OUT` → `ADD_2.IN1` (AdapterConnection): the value 5 is applied to the first summand.
2. `initval_AI_1.OUT` → `ADD_2.IN2` (AdapterConnection): the value 3 is applied to the second summand.

The output `ADD_2.OUT` is not connected any further; the result (5 + 3 = 8) is present there and can be observed at runtime.

## Summary

The exercise `Uebung_000_AX` is the simplest calculation in the exercise set: two `initval_AI` blocks provide the constants 5 and 3, and `AI_ADD_2` adds them via adapter connections to 8. It is the basis for any kind of counters, offsets or scalings in a control system.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
