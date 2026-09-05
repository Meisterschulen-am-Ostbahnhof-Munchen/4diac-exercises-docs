# Training_01_OPC_UA_SUB: Distributed I1→Q1 over OPC-UA ("SUB style")

![Training_01_OPC_UA_SUB_network](./Training_01_OPC_UA_SUB_network.svg)

* * * * * * * * * *

## Introduction

`Training_01_OPC_UA_SUB` shows the same use case as `Training_02_OPC_UA_RES`
(`Input_I1` on Device A → `Output_Q1` on Device B, distributed over
OPC-UA) - but as the **counterpart design**: the **"SUB style"**. Instead
of placing the communication blocks in each device's `<Resource>`, the
OPC-UA protocol here lives directly inside a reusable `MyLib::sys`
composite. The name comes from "SUB" for `subapp::Training_01_OPC_UA_SUB`,
the package holding both device composites. Per the comment in the source
blocks, this pattern matches a **genuine production pattern** ("harvester",
`Krauternter`) from real practice.

## Function Blocks Used

| Instance | Location | Type | Purpose |
|---|---|---|---|
| `SubApp_PC_A` | Application (`App_OPC_UA_SUB`) | `subapp::Training_01_OPC_UA_SUB::SubApp_PC_A` | Fully encapsulates Device A's logic |
| `SubApp_PC_B` | Application | `subapp::Training_01_OPC_UA_SUB::SubApp_PC_B` | Fully encapsulates Device B's logic |
| `INPUT_I1` (inside `SubApp_PC_A`) | — | `MyLib::sys::logiBUS_IXA_TO_CLIENT_OPC` | Reads `Input_I1` **and** actively writes via OPC-UA (`CLIENT_1_0`) in one block |
| `OUTPUT_Q1` (inside `SubApp_PC_B`) | — | `MyLib::sys::logiBUS_QXA_FROM_SUBSCRIBE_OPC` | Drives `Output_Q1` based on a locally monitored OPC-UA node (`SUBSCRIBE_1`) in one block |

Both device Resources (`EMB_RES_A`, `EMB_RES_B`) contain **nothing** but a
generic `E_TRIG` (`EVENTTYPE='EInit'`) - unlike `Training_02_OPC_UA_RES`,
where `CLIENT_Q1`/`SUBSCRIBE_Q1` sit explicitly in the Resource. All
protocol knowledge here is encapsulated inside
`logiBUS_IXA_TO_CLIENT_OPC`/`logiBUS_QXA_FROM_SUBSCRIBE_OPC`.

## OPC-UA Address Space

Same constants as in `Training_02_OPC_UA_RES`, from
`VV::const::OPC_UA::myOpcUaAddresses`:

| Constant | Used by |
|---|---|
| `Q1_REMOTE_WRITE` | `SubApp_PC_A.INPUT_I1` (`ID` parameter) |
| `Q1_LOCAL_READ` | `SubApp_PC_B.OUTPUT_Q1` (`ID` parameter) |

## Program Flow and Connections

1. **Application** (`App_OPC_UA_SUB`): instantiates `SubApp_PC_A` and
   `SubApp_PC_B` directly, with **no** connection between them at all - the
   entire coupling happens purely as OPC-UA communication at runtime,
   there is no model connection between the two composites.
2. **`SubApp_PC_A`**: a single instance `INPUT_I1`
   (`logiBUS_IXA_TO_CLIENT_OPC`, `Input=Input_I1`, `ID=Q1_REMOTE_WRITE`) -
   reads the physical input and actively writes its state via OPC-UA
   remote write, all inside one composite block.
3. **`SubApp_PC_B`**: mirrored, a single instance `OUTPUT_Q1`
   (`logiBUS_QXA_FROM_SUBSCRIBE_OPC`, `Output=Output_Q1`,
   `ID=Q1_LOCAL_READ`) - locally monitors the node written by Device A and
   drives the physical output, likewise inside one composite block.
4. **Mapping**: `SubApp_PC_A` → `FORTE_PC_A.EMB_RES_A`, `SubApp_PC_B` →
   `FORTE_PC_B.EMB_RES_B`. The Resources themselves carry no application
   logic at all - just the generic `EInit` trigger that 4diac provides for
   every resource anyway.

## Technical Notes

- **Protocol inside the composite instead of the Resource**: the entire
  difference from `Training_02_OPC_UA_RES` is WHERE the OPC-UA knowledge
  lives - here in a single reusable `MyLib::sys` block per device side,
  there split between an Application FB and a separate Resource adapter
  block. Functionally identical, but the "SUB style" variant is directly
  reusable as a finished block, without every new project having to rebuild
  the Resource wiring from scratch.
- **Real production pattern**: per the comment in `SubApp_PC_A`/
  `SubApp_PC_B`, this is not a teaching example but a pattern actually used
  in practice ("Krauternter"/harvester).
- **Empty Resources**: since all the logic lives in the composite, both
  Resources are left with just the mandatory `EInit` trigger - a clear
  structural contrast to the RES variant.

## Learning Objectives

- Two equivalent architecture patterns for the same distributed OPC-UA
  task: protocol in the Resource ("RES style") vs. protocol in a reusable
  composite ("SUB style").
- Trade-offs of encapsulation: reusability of the finished composite block
  versus transparency of the individual protocol steps in the Resource.
- Connection to a genuine production pattern rather than a purely
  didactic example.

**Difficulty**: Intermediate
**Prerequisites**: `Training_02_OPC_UA_RES` (the "RES style" counterpart),
basics of the OPC-UA adapter blocks.

## Summary

`Training_01_OPC_UA_SUB` solves the same distribution task as
`Training_02_OPC_UA_RES`, but moves the OPC-UA protocol knowledge entirely
into reusable `MyLib::sys` composites instead of wiring it into each
device's Resource - a real, practice-proven architecture pattern.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
