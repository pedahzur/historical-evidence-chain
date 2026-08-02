# Source-Bound Extraction Prompt

**Prompt ID:** `SAL-PROMPT-EXTRACTION-1.0`<br>
**Codebook:** `SAL-CB-0.3`<br>
**Vocabulary:** `SAL-CV-1.0`<br>
**Output contract:** `SAL-EXTRACTION-JSON-1.1`

## Inputs

- `document_id`, `transcription_id`, and immutable transcription version
- line-addressable transcription produced under `SAL-PROMPT-TRANSCRIPTION-1.0`
- supplied document metadata and `SAL-CV-1.0`

## Evidence rules

1. Extract only what the supplied source/transcription supports. Do not use outside historical knowledge.
2. Create a passage for every consequential observation. Each passage names its `source_id`, line range, transcription ID, and an exact quote.
3. Preserve surface/source forms separately from normalized values. Every normalized value has `epistemic_status: normalized` and a `normalization_basis` naming the rule or authority. Never overwrite source wording.
4. Every role, event, event participant, relation, and Claim in Source has exactly one `epistemic_status`: `source_stated`, `normalized`, or `inferred`. Inference requires cited source-stated basis, rationale, and certainty.
5. Every record below `high` certainty has one or more `uncertainty_reasons`.
6. Roles are document-specific. A shared name/surname does not establish identity or kinship; co-occurrence does not establish association.
7. A warrant does not establish arrest, a summons or witness list does not establish testimony, and an indictment does not establish conviction.
8. An order and its execution are separate events. Preserve negation, modality, attribution, and procedural stage.
9. Treat every supernatural allegation as an attributed Claim in Source, not an unqualified event. If no claimant is stated, set `claimant_not_stated: true`.
10. Relationships require explicit relational evidence. Possible associations belong in warnings, not relationship records.
11. Use the controlled `passage_type`, `event_participant_role`, `direction`,
    `polarity`, and `modality` values from `SAL-CV-1.0`. Set `status` from
    `extraction_operation_status`.
12. Return valid JSON only. Use local IDs (`PAS-LOCAL-001`); stable five-digit IDs are assigned at controlled ingest.

## Output

```json
{
  "prompt_id": "SAL-PROMPT-EXTRACTION-1.0",
  "contract_version": "SAL-EXTRACTION-JSON-1.1",
  "operation_class": "extraction",
  "document_id": "DOC-00001",
  "transcription_id": "TRN-00001",
  "status": "complete|partial|cannot_extract",
  "passages": [
    {
      "passage_id": "PAS-LOCAL-001",
      "source_id": "SRC-00001",
      "line_start": "SRC-00001-L003",
      "line_end": "SRC-00001-L005",
      "quote_exact": "exact transcription text",
      "region": "body",
      "passage_type": "body|heading|margin|endorsement|signature|other"
    }
  ],
  "entities": [
    {
      "entity_id": "ENT-LOCAL-001",
      "entity_type": "person|group|organization|court|government_body|place|document|legal_office|other|unknown",
      "preferred_label": null,
      "epistemic_status": "source_stated|normalized|inferred",
      "normalization_basis": null,
      "normalization_note": null,
      "certainty": "high|medium|low",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "mentions": [
    {
      "mention_id": "MEN-LOCAL-001",
      "passage_id": "PAS-LOCAL-001",
      "entity_id": "ENT-LOCAL-001",
      "surface_form": "exact source form",
      "entity_type": "person",
      "normalized_label": null,
      "normalization_basis": null,
      "epistemic_status": "source_stated",
      "certainty": "high",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "roles": [
    {
      "role_id": "ROL-LOCAL-001",
      "passage_id": "PAS-LOCAL-001",
      "mention_id": "MEN-LOCAL-001",
      "entity_id": "ENT-LOCAL-001",
      "role": "participant_role code",
      "role_as_written": null,
      "epistemic_status": "source_stated",
      "certainty": "high",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "events": [
    {
      "event_id": "EVT-LOCAL-001",
      "passage_id": "PAS-LOCAL-001",
      "event_type": "event_type code",
      "trigger_as_written": "exact wording",
      "date_as_written": null,
      "date_normalized": null,
      "normalization_basis": null,
      "place_as_written": null,
      "epistemic_status": "source_stated",
      "certainty": "high",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "event_participants": [
    {
      "event_participant_id": "EVP-LOCAL-001",
      "event_id": "EVT-LOCAL-001",
      "passage_id": "PAS-LOCAL-001",
      "mention_id": "MEN-LOCAL-001",
      "entity_id": "ENT-LOCAL-001",
      "event_participant_role": "actor|target|declarant|authority|subject|other|unclear",
      "role_as_written": null,
      "epistemic_status": "source_stated",
      "certainty": "high",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "relationships": [
    {
      "relationship_id": "REL-LOCAL-001",
      "passage_id": "PAS-LOCAL-001",
      "subject_id": "ENT-LOCAL-001",
      "predicate": "relation_type code",
      "object_id": "ENT-LOCAL-002",
      "relationship_as_written": "exact relational wording",
      "direction": "directed|undirected",
      "epistemic_status": "source_stated",
      "certainty": "high",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "claims_in_sources": [
    {
      "source_claim_id": "SCL-LOCAL-001",
      "passage_id": "PAS-LOCAL-001",
      "claim_type": "claim_type code",
      "claim_text": "one explicit proposition close to source wording",
      "claimant_id": "ENT-LOCAL-001",
      "claimant_not_stated": false,
      "target_id": "ENT-LOCAL-002",
      "polarity": "affirmed|negated|mixed|unclear",
      "modality": "asserted|reported|possible|ordered|unclear",
      "epistemic_status": "source_stated",
      "certainty": "high",
      "uncertainty_reasons": [],
      "inference_rationale": null
    }
  ],
  "warnings": []
}
```

## Checks before responding

- Every consequential record reaches an exact passage and immutable transcription.
- Every referenced local ID exists.
- Every event participant is an `EVP` record linked to one event, one passage,
  and one entity, with exactly one controlled event-participant role.
- Every claim-in-source is bound directly to one passage and contains one proposition; compound propositions are split.
- Every normalization and inference satisfies its provenance requirements.
- No procedural-stage or supernatural-truth overreach is encoded.
