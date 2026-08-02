# Diplomatic Transcription Prompt

**Prompt ID:** `SAL-PROMPT-TRANSCRIPTION-1.0`<br>
**Codebook:** `SAL-CB-0.3`<br>
**Output contract:** `SAL-TRANSCRIPTION-JSON-1.1`

## Inputs

- `document_id` (`DOC-00001` format)
- ordered `sources`, each with `source_id` (`SRC-00001` format), image, archival locator, and optional surface label
- optional supplied catalog context; it may identify the witness but must not repair unreadable text

## Task

Create a diplomatic transcription of only the supplied source images.

1. Preserve spelling, capitalization where discernible, punctuation, abbreviations, meaningful line breaks, source order, and image boundaries.
2. Do not silently modernize names, dates, legal terms, or grammar. Do not fill text from historical knowledge, another edition, or formulaic expectation.
3. Use these codebook conventions consistently:
   - `[illegible]` or `[illegible: N words]`
   - `[word?]` for a preferred uncertain reading
   - `[word/ward]` for two live alternatives
   - `⟦deleted text⟧`
   - `〈inserted text〉`, with position in the line note
   - `[gap]` for physical loss or missing image area
   - `[…]` only for deliberate omission in an excerpt, never a full transcription
4. Represent marginalia, endorsements, signatures, deletions, insertions, seals, and non-text marks as separate blocks or notes. Do not force reading order when uncertain.
5. Assign line IDs `<source_id>-LNNN` from top to bottom within each source. Assign block IDs `<source_id>-BNN`.
6. For every line below `high` certainty, record at least one controlled `uncertainty_reason` and any alternative reading.
7. Set `status` from `transcription_operation_status` and `block_type` from
   `transcription_block_type` in `SAL-CV-1.0`.
8. Return valid JSON only. Use explicit missingness values from `SAL-CV-1.0`, `null` for structurally optional unavailable scalars, and empty arrays when no items were observed.

## Output

```json
{
  "prompt_id": "SAL-PROMPT-TRANSCRIPTION-1.0",
  "contract_version": "SAL-TRANSCRIPTION-JSON-1.1",
  "operation_class": "transcription",
  "document_id": "DOC-00001",
  "transcription_local_id": "TRN-LOCAL-001",
  "status": "complete|partial|cannot_transcribe",
  "convention_version": "SAL-CB-0.3",
  "sources": [
    {
      "source_id": "SRC-00001",
      "surface_label": "folio/page label or null",
      "blocks": [
        {
          "block_id": "SRC-00001-B01",
          "block_type": "body|heading|margin|interlinear|endorsement|signature|other",
          "reading_order": 1,
          "region": "upper body",
          "lines": [
            {
              "line_id": "SRC-00001-L001",
              "text": "diplomatic text",
              "certainty": "high|medium|low",
              "uncertainty_reasons": [],
              "alternatives": [],
              "notes": []
            }
          ]
        }
      ],
      "non_text_marks": [],
      "source_notes": []
    }
  ],
  "general_notes": [],
  "limitations": []
}
```

## Checks before responding

- Every supplied source is represented in order.
- Every visible text block is transcribed or explicitly marked unavailable.
- Line and block IDs are unique.
- Uncertainty is exposed rather than guessed.
- No extraction, normalization, or historical interpretation appears in the transcription text.
