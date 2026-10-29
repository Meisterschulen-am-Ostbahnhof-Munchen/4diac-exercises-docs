# OPC-UA Addresses (`myOpcUaAddresses.gcf`)

The OPC-UA IDs for the trainings `test_VV/sys/03_OPC_UA/Training_01` to `_18`
are computed in the GCF from a few base values. The functions for this
([`F_OPC_LOCAL`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_LOCAL/), [`F_OPC_REMOTE`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_OPC_REMOTE/), [`F_MGR_TO_OPC`](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/4diac-library-reference-docs/en/latest/Bibliotheken/ExternalLibraries/MyLib_OPC/F_MGR_TO_OPC/)) are part of the `MyLib_OPC` library. The 4diac export evaluates them;
the boot file contains the finished strings.

## Changing Stations: Only Here

```text
PC_A : WSTRING := logiBUS::stations::Computers::C_11;
PC_B : WSTRING := logiBUS::stations::Computers::C_12;
```

- The `.sys` files use `PC_A`/`PC_B` as `MGR_ID` and import
  `VV::const::OPC_UA::myOpcUaAddresses::PC_A`/`PC_B`.
- The OPC-UA endpoints `PC_A_OPC`/`PC_B_OPC` are derived from them with
  `MyLib_OPC::F_MGR_TO_OPC` (`192.168.1.12:61499` ->
  `opc.tcp://192.168.1.12:4840`).

A different station, for example `C_13`, therefore takes effect in the
deployment and in all remote IDs.

## Structure per Signal

```text
Q1_NODE         := "/Objects/DigitalOutput/Q1,1:s=Output_Q1";            // node, once
Q1_LOCAL_READ   := MyLib_OPC::F_OPC_LOCAL("READ", Q1_NODE);               // station that owns the node
Q1_REMOTE_WRITE := MyLib_OPC::F_OPC_REMOTE("WRITE", PC_B_OPC, Q1_NODE);   // other station
```

| Function | Result |
|---|---|
| `F_OPC_LOCAL(action, node)` | `opc_ua[ACTION;NODE]` |
| `F_OPC_REMOTE(action, endpoint, node)` | `opc_ua[ACTION;ENDPOINT#;NODE]` |

Matching actions (see the 4diac documentation `opcua.adoc`):

| Local | Matching FB (local) | Remote | Matching FB (remote) |
|---|---|---|---|
| `READ` | `AX_SUBSCRIBE_1` / `SUBSCRIBE_n` | `WRITE` | `CLIENT_n_0` |
| `CREATE_METHOD` | `SERVER_0` | `CALL_METHOD` | `CLIENT_0` |

- The name suffix `*_LOCAL_*`/`*_REMOTE_*` has been kept so that the SubApps
  continue to work unchanged.
- `SW2_*` bundles two nodes in one string (separated by `;`) for
  `SUBSCRIBE_2`/`CLIENT_2_0`.

## Notes

- Keep `Comment=` in the GCF short, otherwise the IDE's resave truncates long
  comments. Details belong in this file.
- Verified: the boot files of Training_01 (PC_A and PC_B) are byte-identical
  before and after the change.

---

### 🌐 Related Topic Subpages on ms-muc-docs.de

- [🌐 Eclipse 4diac IDE & Color Reference on ms-muc-docs.de](https://www.ms-muc-docs.de/iec-61499/eclipse-4diac/)
