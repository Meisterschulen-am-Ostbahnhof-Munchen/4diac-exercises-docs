# Overview: CAN exercises (ISOBUS PGN, 126 to 133)

This page assigns the CAN exercises for sending, receiving and requesting to each other: which exercise sends, which receives, and which pairs match. The exercises run on two devices, or on one device against another participant on the bus (for example a PEAK adapter).

## The exercises

| Exercise | Role | Block | Behavior |
| --- | --- | --- | --- |
| [Uebung_126_AX](Uebung_126_AX.md) | Sender | `AlPgnTxNew8Bcycl_REQ` | cyclic (500), answers requests, data via adapter `CB` |
| [Uebung_126b_AX](Uebung_126b_AX.md), [Uebung_126b2_AX](Uebung_126b2_AX.md) | Sender | `AlPgnTxNew8Bcycl_REQ` | like 126, sine function for plotting |
| [Uebung_127_AX](Uebung_127_AX.md) | Sender | `AlPgnTxNew8Bcycl` | cyclic (500), answers requests, fixed data, key I1 triggers an update |
| [Uebung_128_AX](Uebung_128_AX.md) | Sender | `AlPgnTxNew8B` | global, sends on key press I1, does **not** answer requests |
| [Uebung_128b_AX](Uebung_128b_AX.md) | Sender | `AlPgnTxNew_TP` | global via TP-BAM (more than 8 bytes), does not answer requests, **no counterpart** |
| [Uebung_130_AX](Uebung_130_AX.md) | Receiver | `AlPgnRxNew8B` | receives every message of the PGN |
| [Uebung_131_AX](Uebung_131_AX.md) | Receiver | `AlPgnRxNew8Bcylc` | cyclic reception with `u16CtrlTime` = 1500 |
| [Uebung_132_AX](Uebung_132_AX.md) | Receiver, requests | `AlPgnRxNew8B_REQ` | sends a request on key press I1 and receives the response |
| [Uebung_133_AX](Uebung_133_AX.md) | Receiver, requests | `AlPgnRxNew8Bcylc_REQ` | requests cyclically (`u16DefRepRate` = 500), `u16CtrlTime` = 1500 |

All exercises use the PGN `PGN_PDU1_PropA` (Proprietary A, 61184) with 8 data bytes.

## Which counterpart matches which exercise?

A "Yes" means the exercise in the row has the exercise in the column as a counterpart.

| Receiver → / Sender ↓ | [Uebung_130_AX](Uebung_130_AX.md) | [Uebung_131_AX](Uebung_131_AX.md) | [Uebung_132_AX](Uebung_132_AX.md) | [Uebung_133_AX](Uebung_133_AX.md) |
| --- | :---: | :---: | :---: | :---: |
| [Uebung_126_AX](Uebung_126_AX.md), [Uebung_126b_AX](Uebung_126b_AX.md), [Uebung_126b2_AX](Uebung_126b2_AX.md) | Yes | Yes | Yes | Yes |
| [Uebung_127_AX](Uebung_127_AX.md) | Yes | Yes | Yes | Yes |
| [Uebung_128_AX](Uebung_128_AX.md) | Yes | Yes | **No** | **No** |
| [Uebung_128b_AX](Uebung_128b_AX.md) | No | No | No | No |

Notes:

- [Uebung_132_AX](Uebung_132_AX.md) and [Uebung_133_AX](Uebung_133_AX.md) request the PGN (Request for PGN). Only the senders `AlPgnTxNew8B_REQ`, `AlPgnTxNew8Bcycl` and `AlPgnTxNew8Bcycl_REQ` answer that, that is 126, 126b, 126b2 and 127.
- [Uebung_128_AX](Uebung_128_AX.md) (`AlPgnTxNew8B`) and [Uebung_128b_AX](Uebung_128b_AX.md) (`AlPgnTxNew_TP`) do not answer requests.
- [Uebung_131_AX](Uebung_131_AX.md) and [Uebung_133_AX](Uebung_133_AX.md) expect cyclic reception. With [Uebung_128_AX](Uebung_128_AX.md) messages only appear on a key press.
- [Uebung_128b_AX](Uebung_128b_AX.md) sends over the transport protocol. The existing receive blocks only pass 8 bytes on to the application, a receiver for longer PGNs (`AlPgnRxNew_TP`) is in progress.

!!! info "Address and mask"
    The exercises define the remote station via `NmGetCfInfo`: the receivers and 126 use `PEAK_ADD` and `PEAK_FLT`, 127 uses `VT_ADD` and `VT_FLT`. If you couple two devices, check that address and mask match the counterpart.

Background on request, response and transport protocol: [Request for PGN, response and transport protocol](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/isobus-other-docs/en/latest/Request-Antwort-und-Transportprotokoll/).
