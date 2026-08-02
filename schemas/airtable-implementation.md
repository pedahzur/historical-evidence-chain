# Airtable Implementation Specification

**Schema ID:** `SAL-AIRTABLE-1.0`
**Base:** `Salem Historical Evidence Chain`
**Codebook:** `SAL-CB-0.3`
**Vocabulary:** `SAL-CV-1.0`

This is a deployable specification, not a claim that a production base exists.

## Architecture and build rules

One record represents one analytical object. Keep source wording, normalization, inference, audit, and adjudication in separate fields/records. Reviewed transcriptions and audit decisions are append-only. Airtable record IDs are technical only; all links exported for scholarship use the five-digit stable IDs in `SAL-SCHEMA-NOTES-1.0`.

```text
Sources 1---* Document Sources *---1 Documents
Documents 1---* Transcriptions 1---* Passages *---1 Document Sources
Passages 1---* Mentions *---1 Entities
Passages 1---* Roles / Events / Event Participants / Relations
Events 1---* Event Participants *---1 Mentions / Entities
Passages 1---* Claims in Sources
Historical Claims 1---* Claim Dependencies ---> Claims in Sources / Historical Claims
Claims in Sources / Historical Claims 1---* Validations 1---* Adjudications
Entities 1---* Entity Decisions
Model Runs / Coding Records ---> versioned analytical records / Audit Integrity Findings
Prompt-local IDs ---> Ingest ID Map ---> stable scholarly records
Any record or field disagreement ---> Adjudications
```

Controlled values are single selects populated from `metadata/controlled-vocabulary.yaml`. Only genuinely plural values—`uncertainty_reasons`, audit `issue_types`, and adjudication `disagreement_reasons`—are multi-select vocabulary fields. Add Airtable created/modified time and user fields to every table for operations, but exclude them from scholarly exports.

### Append-only analytical-record contract

Every `PAS`, `MEN`, `ROL`, `EVT`, `EVP`, `REL`, `SCL`, `CLM`, `DEP`, and `EDC` record includes the fields below. They are scholarly provenance fields, not Airtable's mutable created-by/modified-by metadata. `VAL` uses the same creator/time/version/supersedes/comparison lineage with its audit-specific status set.

| Field | Type | Rule |
|---|---|---|
| `created_by_run` | LR → Model Runs | Exactly one of `created_by_run` and `created_by_coding`; required for model output |
| `created_by_coding` | LR → Coding Records | Exactly one creator route; required for human output, including a human correction of model output |
| `created_at` | DT | Required immutable UTC timestamp from the originating run/session, not import time |
| `version` | N integer | Required >= 1; unique within a supersession lineage |
| `supersedes` | LR → same table | Empty for v1; otherwise exactly one earlier record for the same logical object; no cycles |
| `status` | SS | `draft`, `submitted`, `reviewed`, `audited`, `adjudicated`, `superseded`, or `withdrawn` |
| `comparison_key` | SLT | Optional steward-assigned key grouping independent human/model alternatives; alternatives never supersede one another merely because they are compared |

The stable ID, creator route, `created_at`, `version`, `supersedes`, and `comparison_key` are immutable after assignment. Substantive content is immutable from `submitted` onward. A correction receives a new stable ID and version, links to its predecessor, and leaves the predecessor in place with a monotonic status transition recorded in the Airtable audit log. Human and model outputs remain separate records; an adjudication may choose or synthesize them but never rewrites either one.

## Tables

The first field is the primary field. `SLT`, `LT`, `SS`, `MS`, `LR`, `DT`, `N`, `CB`, and `URL` mean single-line text, long text, single select, multiple select, linked record, datetime, number, checkbox, and URL.

### `Documents`

| Field | Type | Rule |
|---|---|---|
| `document_id` | SLT | Required, unique, immutable; `^DOC-[0-9]{5}$` |
| `short_title` | SLT | Required editorial label |
| `document_type` | SS | Required vocabulary value |
| `document_type_as_supplied` | SLT | Preserve source/catalog wording |
| `document_type_basis` | SLT | Required: heading, formula, catalog metadata, inferred form, or other |
| `date_as_written` | SLT | Exact wording or explicit missingness |
| `date_normalized` | SLT | ISO date/interval only when justified |
| `normalization_basis` | SS | Required when normalized |
| `place_as_written` | SLT | Exact wording when present |
| `summary` | LT | Optional neutral description |
| `boundary_note` | LT | Required when source/document boundaries are uncertain |
| `workflow_status` | SS | `draft`, `in_coding`, `in_review`, `approved`, `released`, `withdrawn` |
| `codebook_version` | SLT | Required; default `SAL-CB-0.3` |

### `Sources`

One record is one canonical image/surface or other supplied source witness. It is not document-owned and may be reused without duplicating archival metadata.

| Field | Type | Rule |
|---|---|---|
| `source_id` | SLT | Required, unique; `^SRC-[0-9]{5}$` |
| `repository_name` | SLT | Required |
| `collection_name` | SLT | Optional |
| `catalog_reference` | SLT | Required |
| `surface_label` | SLT | Folio/page/side as supplied |
| `source_url` | URL | Stable catalog URL only; never signed/temporary |
| `asset_reference` | SLT | Controlled storage/repository reference, not a public restricted path |
| `asset_sha256` | SLT | Optional 64 lowercase hex for authorized working copy |
| `rights_status` | SS | Required vocabulary value |
| `rights_access_note` | LT | Required |
| `image_quality` | SS | `good`, `mixed`, `poor`, `unusable`, `not_assessed` |
| `source_notes` | LT | Damage, cropping, order, or access note |

### `Document Sources`

This junction is authoritative for the many-to-many Documents–Sources relationship and for document-local ordering. Never copy repository, catalog, rights, URL, or asset fields into this table.

| Field | Type | Rule |
|---|---|---|
| `document_source_id` | SLT | Required, unique, immutable; `^DSR-[0-9]{5}$` |
| `document` | LR → Documents | Required, exactly one |
| `source` | LR → Sources | Required, exactly one |
| `source_order` | N integer | Required, >= 1, unique within document |
| `surface_label_in_document` | SLT | Optional document-local folio/page/side label; does not overwrite the source registry label |
| `boundary_basis` | SS | `catalog_metadata`, `physical_continuity`, `textual_continuity`, `editorial_decision`, `unclear`, or `other` |
| `boundary_note` | LT | Required when one source is linked to multiple documents, boundaries are uncertain, or basis is `other`/`unclear` |
| `created_by_run` / `created_by_coding` | LR | Exactly one durable creator route for the boundary/link decision |
| `created_at` | DT | Required immutable originating timestamp |

### `Transcriptions`

| Field | Type | Rule |
|---|---|---|
| `transcription_id` | SLT | Required, unique; `^TRN-[0-9]{5}$` |
| `document` | LR → Documents | Required, exactly one |
| `document_sources` | LR → Document Sources | Required; all links belong to the document and are ordered by junction `source_order` |
| `version` | N integer | Required >= 1; unique within document |
| `supersedes` | LR → Transcriptions | Empty for v1; otherwise one earlier version of same document |
| `status` | SS | `draft`, `reviewed`, `adjudicated`, `superseded` |
| `creator_type` | SS | `human`, `model`, `hybrid` |
| `coding_record` | LR → Coding Records | Required for human/hybrid |
| `model_run` | LR → Model Runs | Required for model/hybrid |
| `prompt_version` | SLT | Required for model/hybrid |
| `convention_version` | SLT | Required |
| `transcription_json` | LT | Required immutable payload; rich text off |
| `transcription_text` | LT | Optional lossless rendered view |
| `general_notes` | LT | Optional |

### `Passages`

| Field | Type | Rule |
|---|---|---|
| `passage_id` | SLT | Required, unique; `^PAS-[0-9]{5}$` |
| `transcription` | LR → Transcriptions | Required, exactly one |
| `document_source` | LR → Document Sources | Required; must belong to the transcription document and appear in its `document_sources` |
| `source` | Lookup via Document Sources | Canonical SRC used in line IDs and release exports |
| `region` | SLT | Required when no line locator is possible |
| `line_start` / `line_end` | SLT | `<SRC-ID>-LNNN`; same source; end >= start |
| `quote_exact` | LT | Required exact transcription span |
| `passage_type` | SS | `body`, `heading`, `margin`, `endorsement`, `signature`, `other` |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Passages / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Entities`

| Field | Type | Rule |
|---|---|---|
| `entity_id` | SLT | Required, unique; `^ENT-[0-9]{5}$` |
| `entity_type` | SS | Required vocabulary value |
| `preferred_label` | SLT | Optional when unresolved |
| `epistemic_status` | SS | Required |
| `normalization_basis` | SS | Required for normalized identity/label |
| `normalization_note` | LT | Rule/authority/candidate rationale |
| `inference_rationale` | LT | Required when any canonical identity, type, label, merge, split, or authority match is inferred; cite source-stated basis |
| `authority_uri` | URL | Optional persistent authority URI |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless certainty is high |
| `entity_status` | SS | `provisional`, `resolved`, `merged`, `split`, `withdrawn` |
| `replaced_by` | LR → Entities | Required for merge/withdrawal when applicable |
| `current_decisions` | LR → Entity Decisions | Steward-maintained links to current reviewed/adjudicated decisions; not a substitute for decision history |

### `Entity Decisions`

Canonical entities remain stable registry anchors. Every proposed identification, mention link, preferred-label/type choice, authority match, merge, split, or status change is an independently attributable, append-only decision so human and model proposals can be compared before the registry is updated.

| Field | Type | Rule |
|---|---|---|
| `entity_decision_id` | SLT | Required, unique; `^EDC-[0-9]{5}$` |
| `entity` | LR → Entities | Required except a `create_entity` proposal not yet assigned an ENT ID |
| `decision_type` | SS | `create_entity`, `link_mention`, `preferred_label`, `entity_type`, `authority_match`, `merge`, `split`, `status_change`, or `other` |
| `mention` | LR → Mentions | Required for `link_mention`; optional supporting context otherwise |
| `decision_value_json` | LT | Required valid JSON snapshot of proposed value(s)/target ID(s); immutable from submission |
| `evidence_passages` | LR → Passages | Required for source-dependent identity decisions |
| `epistemic_status` | SS | Required |
| `normalization_basis` | SS | Required when normalized |
| `inference_rationale` | LT | Required when inferred and for every merge/split; cite the source-stated basis |
| `certainty` / `uncertainty_reasons` | SS / MS | Certainty required; reasons required unless high |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Entity Decisions / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional key grouping competing human/model decisions about the same entity question |

### `Mentions`

| Field | Type | Rule |
|---|---|---|
| `mention_id` | SLT | Required, unique; `^MEN-[0-9]{5}$` |
| `passage` | LR → Passages | Required, exactly one |
| `entity` | LR → Entities | Optional only when identity unresolved |
| `surface_form` | SLT | Required exact transcription form |
| `entity_type` | SS | Required |
| `normalized_label` | SLT | Optional; never replaces surface form |
| `normalization_basis` | SS | Required when normalized label supplied |
| `epistemic_status` | SS | Required; normally source-stated |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `inference_rationale` | LT | Required when inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Mentions / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Roles`

| Field | Type | Rule |
|---|---|---|
| `role_id` | SLT | Required, unique; `^ROL-[0-9]{5}$` |
| `document` | LR → Documents | Required |
| `passage` | LR → Passages | Required |
| `mention` | LR → Mentions | Required |
| `entity` | Lookup | Must match linked mention |
| `role` | SS | Required participant-role vocabulary |
| `role_as_written` | SLT | Exact wording when present |
| `epistemic_status` | SS | Required |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `inference_rationale` | LT | Required when inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Roles / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Events`

| Field | Type | Rule |
|---|---|---|
| `event_id` | SLT | Required, unique; `^EVT-[0-9]{5}$` |
| `document` / `passage` | LR | Both required; exactly one each |
| `event_type` | SS | Required event vocabulary |
| `trigger_as_written` | SLT | Required exact wording |
| `event_participants` | Backlinks from Event Participants | Derived navigation only; participant coding lives in the junction table |
| `date_as_written` / `place_as_written` | SLT | Preserve source forms |
| `date_normalized` / `place_entity` | SLT / LR | Optional normalized values |
| `normalization_basis` | SS | Required for normalized date/place |
| `epistemic_status` | SS | Required |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `inference_rationale` | LT | Required when inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Events / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Event Participants`

One record links one event to one document-local participant role. Do not store participant IDs as a JSON/list field on `Events`, and do not use a generic relation in place of this junction.

| Field | Type | Rule |
|---|---|---|
| `event_participant_id` | SLT | Required, unique; `^EVP-[0-9]{5}$` |
| `event` | LR → Events | Required, exactly one |
| `passage` | LR → Passages | Required exact evidence for this participation/role; same document as event |
| `mention` | LR → Mentions | Required when a source-form participant mention exists |
| `entity` | Lookup from Mention or LR → Entities | Lookup when mention is linked; direct entity link allowed only when no mention exists and must be explained |
| `event_participant_role` | SS | Required `event_participant_role` vocabulary value (`actor`, `target`, `declarant`, `authority`, `subject`, `other`, or `unclear`) |
| `role_as_written` | SLT | Exact source wording when present; explicit missingness otherwise |
| `epistemic_status` | SS | Required |
| `certainty` / `uncertainty_reasons` | SS / MS | Certainty required; reasons required unless high |
| `inference_rationale` | LT | Required when role or participation is inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Event Participants / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Relations`

| Field | Type | Rule |
|---|---|---|
| `relationship_id` | SLT | Required, unique; `^REL-[0-9]{5}$` |
| `passage` | LR → Passages | Required exact evidence |
| `subject_type` / `object_type` | SS | `document`, `entity`, `event`, `mention` |
| `subject_*` / `object_*` | LR to matching table | Exactly one subject and one object link; match declared type |
| `predicate` | SS | Required relation vocabulary |
| `relationship_as_written` | SLT | Required when source-stated |
| `direction` | SS | `directed`, `undirected` |
| `epistemic_status` | SS | Required |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `inference_rationale` | LT | Required when inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Relations / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Claims in Sources`

| Field | Type | Rule |
|---|---|---|
| `source_claim_id` | SLT | Required, unique; `^SCL-[0-9]{5}$` |
| `document` | LR → Documents | Required |
| `passage` | LR → Passages | Required, exactly one direct evidence span |
| `claim_type` | SS | Required vocabulary value |
| `claim_text` | LT | Required; one source-bound proposition; immutable once submitted to audit |
| `claimant_entity` / `target_entity` | LR → Entities | Optional when not stated |
| `claimant_not_stated` | CB | Required when no claimant is named |
| `polarity` | SS | `affirmed`, `negated`, `mixed`, `unclear` |
| `modality` | SS | `asserted`, `reported`, `possible`, `ordered`, `unclear` |
| `epistemic_status` | SS | Normally `source_stated`; any normalized/inferred formulation requires full provenance |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `inference_rationale` | LT | Required when inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Claims in Sources / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Historical Claims`

| Field | Type | Rule |
|---|---|---|
| `historical_claim_id` | SLT | Required, unique; `^CLM-[0-9]{5}$` |
| `claim_text` | LT | Required; one downstream proposition; immutable once submitted to audit |
| `claim_type` | SS | Required vocabulary value |
| `epistemic_status` | SS | Required |
| `normalization_basis` | SS | Required when normalized |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `inference_rationale` | LT | Required when inferred |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Historical Claims / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional human/model alignment key |

### `Claim Dependencies`

| Field | Type | Rule |
|---|---|---|
| `dependency_id` | SLT | Required, unique; `^DEP-[0-9]{5}$` |
| `historical_claim` | LR → Historical Claims | Required, exactly one dependent CLM |
| `support_type` | SS | Required: `SCL` or `CLM` |
| `supporting_source_claim` | LR → Claims in Sources | Required only when support type is SCL |
| `supporting_historical_claim` | LR → Historical Claims | Required only when support type is CLM; cannot self-link |
| `dependency_type` | SS | `supports`, `qualifies`, `contradicts`, `normalization`, `identity_resolution`, `procedural_sequence` |
| `rationale` | LT | Required; every dependency chain must terminate in at least one passage-bound SCL |
| `created_by_run` / `created_by_coding` | LR | Exactly one route; apply append-only contract |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` / `status` | N / LR → Claim Dependencies / SS | Required append-only lineage and lifecycle fields |
| `comparison_key` | SLT | Optional key aligning competing human/model support assertions |

### `Model Runs`

| Field | Type | Rule |
|---|---|---|
| `run_id` | SLT | Required, unique; `^RUN-[0-9]{5}$` |
| `document` | LR → Documents | Required |
| `operation_class` | SS | Required: transcription, extraction, audit |
| `prompt_version` / `provider` / `model` | SLT | Required |
| `parameters_json` | LT | Required valid JSON; include exposed seed/temperature |
| `input_sha256` / `output_sha256` | SLT | 64 lowercase hex; output required on success |
| `started_at` / `ended_at` | DT | Required as applicable |
| `status` | SS | `queued`, `running`, `succeeded`, `failed`, `cancelled` |
| `error_note` | LT | Required on failure/cancellation |

### `Coding Records`

| Field | Type | Rule |
|---|---|---|
| `coding_id` | SLT | Required, unique; `^COD-[0-9]{5}$` |
| `document` | LR → Documents | Required |
| `coder_id` | SLT | Required pseudonymous ID |
| `operation_class` | SS | Required |
| `form_version` / `codebook_version` | SLT | Required |
| `started_at` / `ended_at` | DT | Required as applicable; end >= start |
| `independent_complete` | CB | Must be checked before reconciliation |
| `notes` | LT | Optional |

### `Ingest ID Map`

One immutable record preserves one controlled assignment from a prompt-local identifier to the stable scholarly identifier created at ingest. This table is authoritative evidence of the assignment; mappings are never overwritten, deleted, or reused. A later correction to the analytical object follows that object's `supersedes` chain while this row continues to identify the originally ingested stable record.

| Field | Type | Rule |
|---|---|---|
| `ingest_id_map_id` | SLT | Required, unique, immutable; `^IDM-[0-9]{5}$` |
| `run` | LR → Model Runs | Required originating RUN; document and prompt must agree with this mapping |
| `document` | LR → Documents | Required, exactly one; must match RUN and stable target context |
| `prompt_id` | SLT | Required exact prompt identifier used by RUN |
| `contract_version` | SLT | Required exact output-contract identifier/version used to interpret the local ID |
| `local_id` | SLT | Required exact prompt-emitted local ID; preserve case and punctuation |
| `local_record_type` | SS | Required declared object type/prefix for the local ID |
| `stable_id` | SLT | Required assigned five-digit stable ID; must resolve to exactly one target table/record |
| `stable_record_type` | SS | Required; must equal `local_record_type` and match `stable_id` prefix/table |
| `mapped_at` | DT | Required immutable UTC assignment timestamp |
| `steward_coding` | LR → Coding Records | Required COD identifying the pseudonymous steward/session that approved the mapping; same document |
| `mapping_key` | Formula | Concatenate RUN ID, local type, and local ID; must be globally unique in this table |

Enforce one row per `(run, local_record_type, local_id)`. Within a run, one local ID cannot map to multiple stable IDs and two newly created local IDs cannot receive the same stable ID. RUN, DOC, prompt, contract, COD, type, prefix, table, and target existence are checked before approval and again after export. References to an existing stable ID are not new mappings and remain ordinary foreign keys in the ingested record.

### `Validations`

One immutable record per audited claim and audit pass.

| Field | Type | Rule |
|---|---|---|
| `validation_id` | SLT | Required, unique; `^VAL-[0-9]{5}$` |
| `claim_record_type` | SS | Required: `SCL` or `CLM` |
| `claim_in_source` | LR → Claims in Sources | Exactly one target when type is SCL |
| `historical_claim` | LR → Historical Claims | Exactly one target when type is CLM |
| `created_by_run` / `created_by_coding` | LR | Exactly one durable auditor provenance route |
| `created_at` | DT | Required immutable originating audit timestamp |
| `version` | N integer | Required >=1; unique within validation supersession lineage |
| `supersedes` | LR → Validations | Empty for v1; otherwise exactly one earlier VAL for the same claim and audit assignment; no cycles |
| `comparison_key` | SLT | Required when aligning independent human/model validations of the same claim/audit pass |
| `claim_text_as_audited` | LT | Required frozen copy/hash-checked against claim |
| `decision` | SS | Required audit-decision vocabulary |
| `passages_checked` | LR → Passages | Required unless not assessable for missing evidence |
| `dependencies_checked` | LR → Claim Dependencies | Required for CLM when dependencies exist |
| `contradicting_passages` | LR → Passages | Required when contradicted |
| `issue_types` | MS | Required unless supported |
| `explanation` | LT | Required unless supported |
| `minimal_revision` | LT | Required when a supported weaker formulation is possible |
| `certainty` | SS | Required |
| `uncertainty_reasons` | MS | Required unless high |
| `needs_adjudication` | CB | Required |
| `status` | SS | Required: `open`, `reviewed`, `adjudicated`, or `superseded`; a successor VAL sets its preserved predecessor to `superseded` through a logged monotonic transition |

### `Audit Integrity Findings`

One immutable record stores one `integrity_findings[]` item from `SAL-PROMPT-AUDIT-1.0` / `SAL-AUDIT-JSON-1.1`. These findings concern package integrity (references, locators, quotes, versions, and vocabulary) and do not replace per-claim `Validations`.

| Field | Type | Rule |
|---|---|---|
| `audit_integrity_finding_id` | SLT | Required, unique; `^AIF-[0-9]{5}$` |
| `document` | LR → Documents | Required audit context |
| `transcription` | LR → Transcriptions | Required when the audited package includes a transcription |
| `finding_type` | SS | Required: `wrong_source`, `broken_locator`, `quote_mismatch`, `invalid_reference`, `versioning_error`, `vocabulary_error`, or `other` |
| `target_record_type` | SS | Required stable-ID object type/prefix |
| `target_id` | SLT | Required stable target ID from the prompt; prefix must match type and resolve when the record exists |
| `source_id` | LR → Sources | Prompt-native field; required when the finding names a source |
| `line_start` / `line_end` | SLT | Conditional `<SRC-ID>-LNNN`; both or neither; same source; end >= start |
| `evidence` | LT | Required minimal exact span or structured evidence supplied by the audit prompt; use explicit missingness when unavailable |
| `explanation` | LT | Required explanation of the integrity failure |
| `needs_human_review` | CB | Required |
| `created_by_run` / `created_by_coding` | LR | Exactly one auditor route |
| `created_at` | DT | Required immutable originating timestamp |
| `version` / `supersedes` | N / LR → Audit Integrity Findings | Version >= 1; corrections append and link to the earlier finding |
| `status` | SS | Required: `open`, `confirmed`, `dismissed`, `resolved`, or `superseded` |
| `resolution_note` | LT | Required when dismissed or resolved; never rewrite the original evidence/explanation |
| `adjudication` | LR → Adjudications | Optional resolution of a disputed finding |

### `Adjudications`

An adjudication can resolve any frozen record-level or field-level disagreement. Claim-validation disputes remain a stricter specialization and retain links to their original `VAL` records.

| Field | Type | Rule |
|---|---|---|
| `adjudication_id` | SLT | Required, unique; `^ADJ-[0-9]{5}$` |
| `adjudication_scope` | SS | Required: `record`, `field`, or `claim_validation` |
| `target_record_type` | SS | Required stable-ID object type/prefix; `SCL` or `CLM` for claim validation |
| `target_record_id` | SLT | Required stable target ID; must resolve and match declared type |
| `target_field` | SLT | Required for `field`; blank for whole-record or claim-validation disputes |
| `compared_record_ids` | LT | Required JSON array for record disagreements; preserves every alternative stable ID |
| `candidate_values_json` | LT | Required frozen JSON object for field disagreements, keyed by compared record/creator; never store only the winning value |
| `validations` | LR → Validations | Required for `claim_validation`: two or more for disagreement or one for escalation; otherwise optional supporting inputs |
| `claim_record_type` | SS | Required only for `claim_validation`: `SCL` or `CLM`; must equal target type |
| `claim_in_source` / `historical_claim` | Corresponding LR | Exactly one only for `claim_validation`; all linked validations must concern it and match `target_record_id` |
| `adjudicator_coding` | LR → Coding Records | Required human adjudication route; coding record identifies the pseudonymous adjudicator and session |
| `created_at` | DT | Required immutable UTC adjudication timestamp |
| `disagreement_reasons` | MS | Required vocabulary value(s) |
| `evidence_passages` | LR → Passages | Required when source evidence can resolve the disagreement |
| `resolution_type` | SS | Required: `accept_record`, `accept_value`, `synthesize_new_version`, `retain_alternatives`, or `unresolved` |
| `adjudicated_value_json` | LT | Required for resolved field/record decisions; valid JSON snapshot |
| `claim_audit_resolution` | SS | Required for every `claim_validation`; audit-decision vocabulary or `unresolved` |
| `rationale` | LT | Required |
| `requires_recoding_elsewhere` | SS | `yes`, `no`, or `unknown`; if yes, identify affected records in rationale |
| `version` / `supersedes` | N / LR → Adjudications | Version >= 1; revised adjudication appends and preserves predecessor |
| `status` | SS | `open`, `resolved`, `unresolved`, or `superseded` |
| `resolved_at` | DT | Required when resolved |

## Validation and release controls

Create steward-only `DQ —` views for duplicate/malformed IDs, missing required values, broken/cross-document links, duplicate document–source pairs/order/transcription version, source metadata copied into a junction, a transcription or passage using a source outside its `DSR` links, and quote-locator mismatch. Add views for a missing/dual creator route or `created_at` on any versioned analytical record, DEP, or VAL; invalid/nonlinear version and supersession chains; a later version that overwrote or reused its predecessor ID; normalized value without basis; inferred entity/entity decision/analytical value without rationale; non-high certainty without uncertainty reason; and parallel human/model records incorrectly linked as supersessions rather than grouped by `comparison_key`.

Also flag a relationship without explicit basis, event/procedural overreach, `EVT` with embedded participant lists, `EVP` without event/participant role/passage or with cross-document evidence, SCL without a direct passage, CLM without a dependency chain terminating in SCL, unversioned DEP, contradicted VAL without contradicting passage, VAL successor/predecessor claim mismatch, audited claim without exactly one active decision per auditor/audit pass, malformed/incomplete AIF, resolved AIF without a resolution note, and restricted binary attachments. Generic adjudication views must catch missing targets/field snapshots, and claim-validation adjudications must additionally catch mismatched claims or validations and missing claim audit resolution.

`DQ — IDM` views must flag duplicate `(RUN, local type, local ID)` keys, a local ID mapped more than once, a newly created stable ID assigned to multiple local IDs, local/stable type mismatch, stable-prefix mismatch, missing target, RUN/DOC/prompt/contract mismatch, and a steward COD from the wrong document. Mapping rows are immutable after approval.

Automations may route review and create new versions but must never overwrite a submitted analytical record, approved transcription, entity decision, dependency, validation, ingest ID mapping, AIF evidence, or adjudication. Stable IDs are assigned by a steward-controlled ingest process that writes the corresponding IDM row atomically; temporary UI records cannot become approved. Quote equality, line existence, cross-project/source membership, polymorphic target resolution, IDM uniqueness/referential integrity, graph cycles, version-lineage consistency, immutable-field comparison, and cross-table uniqueness require an external export validator.

Release one UTF-8 CSV per table, including `Ingest ID Map`, plus a manifest containing schema/codebook/vocabulary/prompt/form/contract versions, export time, row counts, file SHA-256 hashes, validator version, IDM integrity result, and unresolved limitations. Exclude Airtable IDs, user emails, attachment tokens, restricted URLs, and archival image binaries.
