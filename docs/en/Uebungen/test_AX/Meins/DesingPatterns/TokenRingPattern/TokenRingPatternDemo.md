# TokenRingPatternDemo: Token Ring Pattern with Five Nodes

![TokenRingPatternDemo_network](./TokenRingPatternDemo_network.svg)

* * * * * * * * * *

## Introduction

`TokenRingPatternDemo` is the demo for the **mutual exclusion / token ring pattern** (IEC 61499 primer course, Module 6, Valeriy Vyatkin). According to the source, version 1.1 expanded it from a pair of nodes to a **real ring with five nodes** (`NodeA`…`NodeE`), because two nodes are just a back-and-forth, not a ring.

### SubApp interface

| Name | Kind | Type | Description |
|---|---|---|---|
| `INIT` / `INITO` | Event | `EInit` | Initialization / confirmation |
| `SEED` | Event input | `Event` | Bootstrap the ring: inject the token at `NodeA` once, after `INIT` |
| `REQUEST_A` … `REQUEST_E` | Event inputs | `Event` | Node requests the critical section for its next token hold |
| `ENTERED_A` … `ENTERED_E` | Event outputs | `Event` | Node entered its critical section |
| `QI` / `QO` | Data | `BOOL` | Event qualifiers |
| `NodeX_HeldCount` | Data output | `UINT` | Number of times node X received the token |
| `NodeX_EnteredCount` | Data output | `UINT` | Number of times node X used the critical section |
| `NodeX_PassCount` | Data output | `UINT` | Number of acknowledged hand-offs of node X |

(X = A…E)

## Function Blocks (FBs) Used

| Instance | Type | Explanation |
|---|---|---|
| `NodeA` … `NodeE` | `Meins::DesingPatterns::TokenRingPattern::TokenRingNode` | One node of the ring. Has the adapters `MTXIN` (receives the token from the upstream neighbour, acknowledges receipt) and `MTXOUT` (hands the token to the downstream neighbour, waits for its acknowledgement) of type `adapter::types::bidirectional::TokenRing::TokenRing`. Events: `REQUEST`, `SEED`, `ENTERED`, `EXITED`; counters `HeldCount`, `EnteredCount`, `PassCount` |

Inside `TokenRingNode`, `REQUEST` remembers a wish (`Want`); when the token arrives, the node enters the critical section only if `Want` is set and then passes the token on to its successor. `SEED` pushes the token directly into the hand-off.

## Program Flow and Connections

- **Token path** (adapter connections `MTXOUT` → `MTXIN`): `NodeA` → `NodeB` → `NodeC` → `NodeD` → `NodeE` → back to `NodeA` ("closing the ring").
- **Initialization**: `INIT` → `NodeA.INIT`, then the `INITO` → `INIT` chain via `NodeB`, `NodeC`, `NodeD` up to `NodeE.INITO` → `INITO`; `QI` is passed on node by node up to `QO` accordingly.
- **Operation**: `SEED` → `NodeA.SEED`; `REQUEST_x` → `Node<x>.REQUEST`; `Node<x>.ENTERED` → `ENTERED_x`.
- **Observation**: the counters `HeldCount`, `EnteredCount` and `PassCount` of each node are exposed as outputs of the SubApp.

## Summary

`TokenRingPatternDemo` shows mutual exclusion with a circulating token: only the node holding the token may enter the critical section; a node that wants it remembers its wish and gets its turn on the next token round.

---

### 🌐 Related topic subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & color reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
