# Per-Claim Evidence Audit Prompt

**Prompt ID:** `SAL-PROMPT-AUDIT-1.0`<br>
**Codebook:** `SAL-CB-0.3`<br>
**Vocabulary:** `SAL-CV-1.0`<br>
**Output contract:** `SAL-AUDIT-JSON-1.1`

## Inputs

- supplied source image(s), document metadata, and approved transcription version
- extraction package containing passages and Claim in Source records, plus Historical Claims and dependency records when those are in audit scope
- `SAL-CB-0.3` and `SAL-CV-1.0`

## Task

Audit every submitted claim as a separate proposition. A claim may be a source-bound `SCL` (Claim in Source) or a downstream `CLM` (Historical Claim). Do not silently edit claims or establish historical truth beyond the supplied source.

1. Confirm source/document/transcription identifiers and passage line ranges.
2. Check image-to-transcription fidelity and exact passage quotes.
3. For an `SCL`, inspect its directly linked `PAS`. For a `CLM`, follow its declared `DEP` chain to supporting `SCL` or `CLM` records and ultimately to passages. Then choose exactly one decision: `supported`, `partially_supported`, `unsupported`, `contradicted`, or `not_assessable`.
4. Preserve attribution, negation, modality, and legal stage. Test specifically for warrant→arrest, summons/list→testimony, indictment→conviction, surname→kinship, and co-occurrence→relationship overreach.
5. Check `epistemic_status`. Normalizations require source form and basis; inferences require source-stated basis, rationale, certainty, and uncertainty reason when below `high`.
6. Treat supernatural allegations as attributed claims, never independently established events.
7. Split a compound claim conceptually and mark `compound_claim`; do not give a single blanket supported decision when components differ.
8. A `contradicted` decision must cite the contradicting passage. A decision other than `supported` requires an explanation and, when possible, a minimally supported revision.
9. Set `needs_adjudication` for material paleographic ambiguity, identity conflict, competing procedural interpretations, or consequential model/human disagreement.
10. Set `audit_status` from `audit_operation_status`, `issue_types` from
    `audit_issue_type`, and every integrity `finding_type` from
    `audit_integrity_finding_type` in `SAL-CV-1.0`.
11. Return valid JSON only. Use local `VAL` and `AIF` IDs; controlled ingest assigns stable five-digit IDs.

## Output

```json
{
  "prompt_id": "SAL-PROMPT-AUDIT-1.0",
  "contract_version": "SAL-AUDIT-JSON-1.1",
  "operation_class": "audit",
  "document_id": "DOC-00001",
  "transcription_id": "TRN-00001",
  "audit_status": "complete|partial|cannot_assess",
  "scope": {
    "source_ids_expected": ["SRC-00001"],
    "source_ids_checked": ["SRC-00001"],
    "claim_refs_expected": [{"claim_record_type": "SCL", "claim_id": "SCL-00001"}],
    "claim_refs_checked": [{"claim_record_type": "SCL", "claim_id": "SCL-00001"}],
    "limitations": []
  },
  "claim_decisions": [
    {
      "validation_id": "VAL-LOCAL-001",
      "claim_record_type": "SCL|CLM",
      "claim_id": "SCL-00001",
      "claim_text_as_audited": "immutable submitted proposition",
      "decision": "supported|partially_supported|unsupported|contradicted|not_assessable",
      "cited_passage_ids": ["PAS-00001"],
      "dependency_ids_checked": [],
      "contradicting_passage_ids": [],
      "issue_types": [],
      "explanation": null,
      "minimally_supported_revision": null,
      "certainty": "high|medium|low",
      "uncertainty_reasons": [],
      "needs_adjudication": false,
      "adjudication_reason": null
    }
  ],
  "integrity_findings": [
    {
      "audit_integrity_finding_id": "AIF-LOCAL-001",
      "finding_type": "broken_locator",
      "target_id": "PAS-00001",
      "source_id": "SRC-00001",
      "line_start": "SRC-00001-L003",
      "line_end": "SRC-00001-L003",
      "evidence": "minimal exact span",
      "explanation": "why integrity fails",
      "needs_human_review": true
    }
  ],
  "summary": "brief source-bound audit summary"
}
```

## Checks before responding

- There is exactly one validation decision for every submitted claim.
- Claim-reference sets in `scope` agree with `claim_decisions`.
- Every non-supported decision has an explanation.
- Every contradiction has contradicting evidence.
- Every integrity finding has an `AIF` ID and one controlled
  `audit_integrity_finding_type` value.
- Original claims remain unchanged.
