# Übersicht: CAN-Übungen (ISOBUS-PGN, 126 bis 133)

Diese Seite ordnet die CAN-Übungen mit Senden, Empfangen und Anfragen einander zu: Welche Übung sendet, welche empfängt, und welche Paare passen zusammen. Die Übungen laufen auf zwei Geräten oder auf einem Gerät gegen einen anderen Teilnehmer am Bus (zum Beispiel PEAK-Adapter).

## Die Übungen

| Übung | Rolle | Baustein | Verhalten |
| --- | --- | --- | --- |
| [Uebung_126_AX](Uebung_126_AX.md) | Sender | `AlPgnTxNew8Bcycl_REQ` | zyklisch (500), beantwortet Requests, Daten über Adapter `CB` |
| [Uebung_126b_AX](Uebung_126b_AX.md), [Uebung_126b2_AX](Uebung_126b2_AX.md) | Sender | `AlPgnTxNew8Bcycl_REQ` | wie 126, Sinus-Funktion zum Plotten |
| [Uebung_127_AX](Uebung_127_AX.md) | Sender | `AlPgnTxNew8Bcycl` | zyklisch (500), beantwortet Requests, feste Daten, Taste I1 löst ein Update aus |
| [Uebung_128_AX](Uebung_128_AX.md) | Sender | `AlPgnTxNew8B` | global, sendet auf Tastendruck I1, beantwortet **keine** Requests |
| [Uebung_128b_AX](Uebung_128b_AX.md) | Sender | `AlPgnTxNew_TP` | global per TP-BAM (mehr als 8 Byte), beantwortet keine Requests, **kein Gegenstück** |
| [Uebung_130_AX](Uebung_130_AX.md) | Empfänger | `AlPgnRxNew8B` | empfängt jede Nachricht der PGN |
| [Uebung_131_AX](Uebung_131_AX.md) | Empfänger | `AlPgnRxNew8Bcylc` | zyklischer Empfang mit `u16CtrlTime` = 1500 |
| [Uebung_132_AX](Uebung_132_AX.md) | Empfänger, fragt an | `AlPgnRxNew8B_REQ` | sendet auf Tastendruck I1 einen Request und empfängt die Antwort |
| [Uebung_133_AX](Uebung_133_AX.md) | Empfänger, fragt an | `AlPgnRxNew8Bcylc_REQ` | fragt zyklisch an (`u16DefRepRate` = 500), `u16CtrlTime` = 1500 |

Alle Übungen verwenden die PGN `PGN_PDU1_PropA` (Proprietary A, 61184), mit 8 Datenbyte.

## Welcher Gegenstück passt zu welcher Übung?

Ein „Ja" heißt: Die Übung in der Zeile hat die Übung in der Spalte als Gegenstück.

| Empfänger → / Sender ↓ | [Uebung_130_AX](Uebung_130_AX.md) | [Uebung_131_AX](Uebung_131_AX.md) | [Uebung_132_AX](Uebung_132_AX.md) | [Uebung_133_AX](Uebung_133_AX.md) |
| --- | :---: | :---: | :---: | :---: |
| [Uebung_126_AX](Uebung_126_AX.md), [Uebung_126b_AX](Uebung_126b_AX.md), [Uebung_126b2_AX](Uebung_126b2_AX.md) | Ja | Ja | Ja | Ja |
| [Uebung_127_AX](Uebung_127_AX.md) | Ja | Ja | Ja | Ja |
| [Uebung_128_AX](Uebung_128_AX.md) | Ja | Ja | **Nein** | **Nein** |
| [Uebung_128b_AX](Uebung_128b_AX.md) | Nein | Nein | Nein | Nein |

Erläuterung:

- [Uebung_132_AX](Uebung_132_AX.md) und [Uebung_133_AX](Uebung_133_AX.md) fragen die PGN an (Request for PGN). Das beantworten nur die Sender `AlPgnTxNew8B_REQ`, `AlPgnTxNew8Bcycl` und `AlPgnTxNew8Bcycl_REQ`, also 126, 126b, 126b2 und 127.
- [Uebung_128_AX](Uebung_128_AX.md) (`AlPgnTxNew8B`) und [Uebung_128b_AX](Uebung_128b_AX.md) (`AlPgnTxNew_TP`) beantworten keine Requests.
- [Uebung_131_AX](Uebung_131_AX.md) und [Uebung_133_AX](Uebung_133_AX.md) erwarten den Empfang zyklisch. Mit [Uebung_128_AX](Uebung_128_AX.md) gibt es Nachrichten nur auf Tastendruck.
- [Uebung_128b_AX](Uebung_128b_AX.md) sendet über das Transportprotokoll. Die bisherigen Empfangsbausteine reichen nur 8 Byte an die Anwendung weiter, ein Empfänger für längere PGNs (`AlPgnRxNew_TP`) ist in Arbeit.

!!! info "Adresse und Maske"
    Die Übungen legen die Gegenstelle über `NmGetCfInfo` fest, bei den Empfängern und bei 126 über `PEAK_ADD` und `PEAK_FLT`, bei 127 über `VT_ADD` und `VT_FLT`. Wer zwei Geräte koppelt, muss prüfen, dass Adresse und Maske zum Gegenstück passen.

Hintergrund zu Request, Antwort und Transportprotokoll: [Request for PGN, Antwort und Transportprotokoll](https://meisterschulen-am-ostbahnhof-munchen-docs.readthedocs.io/projects/isobus-other-docs/de/latest/Request-Antwort-und-Transportprotokoll/).
