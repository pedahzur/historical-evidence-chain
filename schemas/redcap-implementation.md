# REDCap Implementation Specification

**Schema ID:** `SAL-REDCAP-1.0`
**Project A:** `SAL_DOCUMENT_CODING`
**Project B:** `SAL_ENTITY_REGISTRY`
**Project C:** `SAL_SOURCE_REGISTRY`
**Codebook:** `SAL-CB-0.3`
**Vocabulary:** `SAL-CV-1.0`

This is an implementation specification, not a claim that REDCap projects already exist.

## Project design

In Project A, one REDCap record is one `DOC-00001` document. `doc_admin` is non-repeating. All other Project A instruments are repeating. Project B has one record per `ENT-00001` entity so cross-document resolution is not duplicated. Project C is the canonical source registry with one record per `SRC-00001` witness. Project A links a document to one or more canonical sources through repeating `document_source` (`DSR`) junction instances; it never duplicates repository, catalog, rights, URL, asset, or source-quality metadata. The same `SRC` may therefore be linked to multiple `DOC` records with a separate document-local order and boundary decision in each.

REDCap repeat-instance numbers, internal record names, and cross-project API keys are technical. All public links use stable five-digit IDs. A minimum-privilege integration service resolves cross-project `SRC`, `ENT`, `RUN`, and `COD` foreign keys and writes no copied registry metadata into Project A.

Enable audit logging, repeating instruments, user roles, record locking/e-signature where available, and API access only for a minimum-privilege service account. Do not use public survey links or store restricted source images unless an institutional rights/security review explicitly approves a protected file instrument.

### Required append-only provenance block

Add the following prefix-specific fields to every `pas`, `men`, `rol`, `evt`, `evp`, `rel`, `scl`, `clm`, `dep`, and entity-decision (`edc`) repeating instance. VAL uses equivalent creator/time/version/supersedes/comparison fields with its audit-specific status set.

| Suffix | Type | Required rule |
|---|---|---|
| `_created_run_fk` | text | Exactly one of `_created_run_fk` / `_created_cod_fk`; RUN required for model output |
| `_created_cod_fk` | text | Exactly one durable creator route; COD required for human output, including a human correction |
| `_created_at` | text/datetime_seconds | Required immutable UTC time from the originating run/session, not import time |
| `_version` | text/integer | Required >=1; unique within a supersession lineage |
| `_supersedes` | text | Empty for v1; otherwise one earlier stable ID of the same type/logical object; no cycles |
| `_status` | dropdown | `draft`, `submitted`, `reviewed`, `audited`, `adjudicated`, `superseded`, `withdrawn` |
| `_comparison_key` | text | Optional steward-assigned key grouping independent human/model alternatives; parallel alternatives do not supersede one another |

For example, a passage uses `pas_created_run_fk`, `pas_created_cod_fk`, `pas_created_at`, `pas_version`, `pas_supersedes`, `pas_status`, and `pas_comparison_key`. The stable ID, origin fields, timestamp, version, supersedes link, and comparison key are immutable after assignment; substantive fields are locked from `submitted` onward. Corrections create a new stable record/version. Human and model results remain separate instances so they can be compared or adjudicated without overwriting either.

## Project A instruments

Types: `text`, `notes`, `dropdown`, `radio`, `checkbox`, `yesno`. Apply REDCap date/integer validation where specified; enforce ID regex, cross-instance links, exact quotes, and graph rules with saved Data Quality rules plus an export validator.

### `doc_admin` (non-repeating)

| Field | Type | Required rule |
|---|---|---|
| `record_id` | text | `^DOC-[0-9]{5}$`; stable document ID |
| `schema_ver` | text, readonly | `SAL-REDCAP-1.0` |
| `codebook_ver` | text | `SAL-CB-0.3` |
| `short_title` | text | Required |
| `doc_type` | dropdown | document-type vocabulary |
| `doc_type_raw` | text | Supplied/source wording |
| `doc_type_basis` | text | Required |
| `date_as_written` | text | Exact form or missingness |
| `date_normalized` | text | ISO date/interval when justified |
| `date_norm_basis` | dropdown | Required when normalized |
| `place_as_written` | text | Optional exact form |
| `boundary_note` | notes | Required when segmentation uncertain |
| `workflow_status` | dropdown | draft, in_coding, in_review, approved, released, withdrawn |

### `document_source`

| Field | Type | Required rule |
|---|---|---|
| `dsr_id` | text | `^DSR-[0-9]{5}$`; globally unique junction ID |
| `dsr_source_fk` | text | Required existing `SRC` in Project C; no source metadata is copied into Project A |
| `dsr_source_order` | text/integer | >=1; unique within document |
| `dsr_surface_label` | text | Optional document-local label; canonical supplied label remains in Project C |
| `dsr_boundary_basis` | dropdown | catalog_metadata, physical_continuity, textual_continuity, editorial_decision, unclear, other |
| `dsr_boundary_note` | notes | Required when SRC is shared across documents, boundaries are uncertain, or basis is other/unclear |
| `dsr_created_run_fk` / `dsr_created_cod_fk` | text | Exactly one durable creator route for the document-boundary/link decision |
| `dsr_created_at` | text/datetime_seconds | Required immutable originating timestamp |

### `transcription`

| Field | Type | Required rule |
|---|---|---|
| `trn_id` | text | `^TRN-[0-9]{5}$`; globally unique |
| `trn_version` | text/integer | >=1; unique within document |
| `trn_supersedes` | text | Earlier TRN ID for version >1 |
| `trn_dsr_ids` | notes | Required ordered JSON array of valid `DSR` IDs in this document; each resolves to one canonical SRC |
| `trn_status` | dropdown | draft, reviewed, adjudicated, superseded |
| `trn_creator` | radio | human, model, hybrid |
| `trn_cod_fk` | text | COD ID required for human/hybrid |
| `trn_run_fk` | text | RUN ID required for model/hybrid |
| `trn_created_at` | text/datetime_seconds | Required immutable originating timestamp |
| `trn_prompt_ver` | text | Required for model/hybrid |
| `trn_conv_ver` | text | Required |
| `trn_json` | notes | Required immutable JSON |
| `trn_text` | notes | Optional lossless rendered view |

### `passage`

| Field | Type | Required rule |
|---|---|---|
| `pas_id` | text | `^PAS-[0-9]{5}$` |
| `pas_trn_fk` | text | Existing TRN ID in record |
| `pas_dsr_fk` | text | Existing DSR ID in record and transcription; its `dsr_source_fk` is the canonical SRC |
| `pas_region` | text | Required when line locator unavailable |
| `line_start` / `line_end` | text | `<SRC-ID>-LNNN`; same source; end >= start |
| `quote_exact` | notes | Required exact transcription span |
| `passage_type` | dropdown | body, heading, margin, endorsement, signature, other |
| provenance block | mixed | Required `pas_*` creator/time/version/supersedes/status fields defined above |

### `mention`

`men_id` (`MEN-00001`), `men_pas_fk`, optional `men_ent_fk`, `surface_form`, `entity_type`, optional `normalized_label`, conditional `norm_basis`, `epistemic_status`, `certainty`, checkbox `uncertainty_reason`, conditional `inference_rationale`, `mention_note`, and the full `men_*` provenance block. Passage is required; source form is never overwritten; uncertainty reason is required unless certainty is high; inferred mentions require a source-stated basis and rationale. Competing human/model mention boundaries or entity links use separate IDs with the same `men_comparison_key`, not a supersedes link.

### `role`

`role_id` (`ROL-00001`), `role_pas_fk`, `role_men_fk`, `role_ent_fk`, controlled `role_type`, `role_as_written`, `epistemic_status`, `certainty`, `uncertainty_reason`, conditional `inference_rationale`, and the full `rol_*` provenance block (`rol_created_run_fk`, `rol_created_cod_fk`, and so on). All IDs must resolve within the document and entity registry; inferred roles require rationale.

### `event`

`event_id` (`EVT-00001`), `event_pas_fk`, controlled `event_type`, `trigger_as_written`, `date_as_written`, `date_normalized`, `place_as_written`, optional `place_ent_fk`, conditional `norm_basis`, `epistemic_status`, `certainty`, `uncertainty_reason`, conditional `inference_rationale`, and the full `evt_*` provenance block. Orders and execution reports are separate instances. Do not embed participant IDs in this instrument; use `event_participant` instances.

### `event_participant`

One repeating instance per event–participant–role assertion:

| Field | Type | Required rule |
|---|---|---|
| `evp_id` | text | `^EVP-[0-9]{5}$`; globally unique |
| `evp_event_fk` | text | Required existing EVT in this document |
| `evp_pas_fk` | text | Required exact evidence passage in the same document/event context |
| `evp_men_fk` | text | Required when a source-form participant mention exists |
| `evp_ent_fk` | text | Derived from MEN when linked; direct ENT allowed only when no mention exists and rationale explains why |
| `event_participant_role` | dropdown | Required controlled `event_participant_role`: actor, target, declarant, authority, subject, other, unclear |
| `role_as_written` | text | Exact wording when present; explicit missingness otherwise |
| `epistemic_status` | dropdown | Required |
| `certainty` / `uncertainty_reason` | dropdown / checkbox | Certainty required; reason required unless high |
| `inference_rationale` | notes | Required when role or participation is inferred |
| provenance block | mixed | Required `evp_*` creator/time/version/supersedes/status fields defined above |

### `relation`

`rel_id` (`REL-00001`), `rel_pas_fk`, `subject_type`, `subject_id`, controlled `predicate`, `object_type`, `object_id`, `rel_as_written`, `direction`, `epistemic_status`, `certainty`, `uncertainty_reason`, conditional `inference_rationale`, and the full `rel_*` provenance block. Target-type/prefix/existence and explicit-relation rules are validated after export.

### `claim_in_source`

`scl_id` (`SCL-00001`), required direct `scl_pas_fk`, `claim_type`, one-proposition `claim_text`, optional `claimant_ent_fk`, yes/no `claimant_not_stated`, optional `target_ent_fk`, `polarity`, `modality`, `epistemic_status`, `certainty`, `uncertainty_reason`, conditional `inference_rationale`, and the full `scl_*` provenance block. The passage and claim-in-source must belong to the same document. Lock at `submitted`; correction creates a new SCL ID/version linked by `scl_supersedes`.

### `historical_claim`

`clm_id` (`CLM-00001`), `claim_type`, one-proposition `claim_text`, `epistemic_status`, conditional `normalization_basis`, `certainty`, conditional `uncertainty_reason`, conditional `inference_rationale`, and the full `clm_*` provenance block. A historical claim is downstream analysis, not a direct source quotation, and must be supported through dependencies. Lock at `submitted`; correction creates a new CLM ID/version linked by `clm_supersedes`.

### `claim_dependency`

`dep_id` (`DEP-00001`), required dependent `dep_clm_fk`, `support_type` (`SCL` or `CLM`), conditional `support_scl_fk`, conditional `support_clm_fk`, `dependency_type` (`supports`, `qualifies`, `contradicts`, `normalization`, `identity_resolution`, `procedural_sequence`), required `dep_rationale`, and the full `dep_*` creator/time/version/supersedes/status/comparison block. Exactly one supporting ID must match `support_type`; self-links and cycles are invalid; every chain must terminate in a passage-bound SCL. Independent human/model dependency assertions remain parallel DEP instances with a shared `dep_comparison_key`; corrections alone use `dep_supersedes`.

### `model_run`

`run_id` (`RUN-00001`), controlled `operation_class`, `prompt_version`, `provider`, `model_id`, `parameters_json`, `input_sha256`, conditional `output_sha256`, `started_at`, conditional `ended_at`, `run_status`, and conditional `error_note`. Hashes use 64 lowercase hex; terminal statuses require end time.

### `coding_record`

`coding_id` (`COD-00001`), pseudonymous `coder_id`, controlled `operation_class`, `form_version`, `codebook_version`, `started_at`, conditional `ended_at`, yes/no `independent_complete`, and notes. Independent completion must be 1 before reconciliation/adjudication.

### `ingest_id_map`

One immutable repeating instance preserves one controlled prompt-local→stable identifier assignment. The instance remains bound to the originally ingested stable target even if a later analytical correction supersedes that target.

| Field | Type | Required rule |
|---|---|---|
| `idm_id` | text | `^IDM-[0-9]{5}$`; globally unique and immutable |
| `idm_run_fk` | text | Required originating RUN in this document; operation/prompt must agree |
| `idm_doc_fk` | text | Required DOC; must equal the Project A record and stable-target context |
| `idm_prompt_id` | text | Required exact prompt identifier used by RUN |
| `idm_contract_version` | text | Required exact output-contract identifier/version used to interpret local IDs |
| `idm_local_id` | text | Required exact emitted local ID; preserve case and punctuation |
| `idm_local_type` | dropdown | Required declared object type/prefix |
| `idm_stable_id` | text | Required assigned five-digit stable ID |
| `idm_stable_type` | dropdown | Required; must equal local type and stable target prefix/type |
| `idm_mapped_at` | text/datetime_seconds | Required immutable UTC assignment time |
| `idm_steward_cod_fk` | text | Required COD for the pseudonymous steward/session approving the mapping; same DOC |
| `idm_mapping_key` | text/calc, readonly | Concatenate RUN, local type, and local ID; globally unique across exported Project A instances |

The external validator enforces unique `(idm_run_fk, idm_local_type, idm_local_id)`, one stable assignment per local key, no reuse of a newly created stable ID across local IDs, local/stable type equality, prefix/type/table agreement, and existence of RUN, DOC, COD, and the stable target in Project A or the appropriate registry project. Existing stable IDs used only as references are foreign keys, not new IDM rows. Approved IDM instances are locked and never overwritten or deleted.

### `validation`

One immutable repeating instance per audited SCL or CLM and audit pass: `validation_id` (`VAL-00001`), `claim_record_type` (`SCL` or `CLM`), conditional `val_scl_fk`, conditional `val_clm_fk`, exactly one of `val_created_run_fk`/`val_created_cod_fk`, required immutable `val_created_at`, integer `val_version` >=1, conditional same-claim/same-audit-assignment `val_supersedes`, conditional `val_comparison_key` for aligned human/model decisions, frozen `claim_text_audited`, controlled `audit_decision`, JSON list `passage_ids_checked`, JSON list `dependency_ids_checked`, JSON list `contradicting_pas_ids`, checkbox `issue_types`, conditional `explanation`, conditional `minimal_revision`, `certainty`, conditional `uncertainty_reason`, yes/no `needs_adjudication`, and `val_status` (`open`, `reviewed`, `adjudicated`, `superseded`). Exactly one claim target must match the declared type. Every assigned claim requires exactly one active validation per auditor/audit pass; contradiction requires a contradicting passage; any non-supported decision requires explanation. A corrected decision creates a new VAL and sets the retained predecessor to `superseded`; independent human/model VALs share a comparison key and do not supersede each other.

### `audit_integrity_finding`

One repeating instance stores one `integrity_findings[]` item from `SAL-PROMPT-AUDIT-1.0` / `SAL-AUDIT-JSON-1.1`; it supplements and never replaces a claim validation.

| Field | Type | Required rule |
|---|---|---|
| `aif_id` | text | `^AIF-[0-9]{5}$`; globally unique |
| `aif_trn_fk` | text | Audited TRN when the package includes one |
| `finding_type` | dropdown | Required: wrong_source, broken_locator, quote_mismatch, invalid_reference, versioning_error, vocabulary_error, other |
| `target_record_type` | dropdown | Stable-ID object type/prefix derived from and consistent with target |
| `target_id` | text | Required prompt-native stable target ID; must resolve when the target exists |
| `source_id` | text | Conditional existing SRC in Project C and linked to this DOC through DSR |
| `line_start` / `line_end` | text | Both or neither; `<SRC-ID>-LNNN`; same source; end >= start |
| `evidence` | notes | Required minimal exact span or structured evidence; use explicit missingness when unavailable |
| `explanation` | notes | Required explanation of failure |
| `needs_human_review` | yesno | Required |
| `aif_created_run_fk` / `aif_created_cod_fk` | text | Exactly one auditor provenance route |
| `aif_created_at` | text/datetime_seconds | Required immutable originating timestamp |
| `aif_version` / `aif_supersedes` | integer / text | Version >=1; correction appends and links to prior AIF; no cycles |
| `aif_status` | dropdown | Required: open, confirmed, dismissed, resolved, superseded |
| `aif_resolution_note` | notes | Required when dismissed/resolved; original finding remains unchanged |
| `aif_adj_fk` | text | Optional ADJ resolving a disputed finding |

### `adjudication`

This generic repeating instrument resolves any frozen record-level or field-level disagreement while retaining claim-validation disputes as a stricter specialization.

| Field | Type | Required rule |
|---|---|---|
| `adj_id` | text | `^ADJ-[0-9]{5}$`; globally unique |
| `adj_scope` | radio | Required: `record`, `field`, or `claim_validation` |
| `adj_target_type` | dropdown | Required stable-ID type/prefix; SCL/CLM for claim validation |
| `adj_target_id` | text | Required stable target ID; must resolve and match declared type |
| `adj_target_field` | text | Required only for field-level disagreement |
| `adj_compared_ids` | notes | Required JSON array for record disagreement; preserves every alternative stable ID |
| `adj_candidate_values` | notes | Required frozen JSON object for field disagreement, keyed by record/creator |
| `adj_validation_ids` | notes | JSON array required for claim validation: >=2 for disagreement or one for escalation |
| `adj_claim_type` | radio | SCL or CLM, required only for claim validation; must equal target type |
| `adj_claim_fk` | text | Required only for claim validation; all VALs must concern this claim |
| `adj_cod_fk` | text | Required COD for human adjudication session/pseudonymous adjudicator |
| `adj_created_at` | text/datetime_seconds | Required immutable UTC timestamp |
| `adj_disagreement_reasons` | checkbox | Required disagreement vocabulary value(s) |
| `adj_evidence_pas_ids` | notes | JSON array required when source evidence can resolve the dispute |
| `adj_resolution_type` | dropdown | Required: accept_record, accept_value, synthesize_new_version, retain_alternatives, unresolved |
| `adj_value_json` | notes | Required valid JSON for resolved record/field decisions |
| `adj_claim_resolution` | dropdown | Required for every claim validation: audit-decision vocabulary or unresolved |
| `adj_rationale` | notes | Required |
| `adj_recoding_elsewhere` | radio | yes, no, unknown; identify affected IDs in rationale when yes |
| `adj_version` / `adj_supersedes` | integer / text | Version >=1; revised adjudication appends and preserves predecessor |
| `adj_status` | dropdown | open, resolved, unresolved, superseded |
| `adj_resolved_at` | text/datetime_seconds | Required when resolved |

Original candidate records, values, and validations remain immutable. A synthesized resolution creates a new versioned analytical record with its own creator route; the ADJ points to it rather than overwriting inputs.

## Project B: entity registry

Primary record `entity_id` matches `^ENT-[0-9]{5}$`. The non-repeating `entity` instrument contains `entity_type`, `preferred_label`, `epistemic_status`, conditional `normalization_basis`, `normalization_note`, conditional `inference_rationale`, `authority_uri`, `certainty`, conditional `uncertainty_reason`, `entity_status` (`provisional`, `resolved`, `merged`, `split`, `withdrawn`), and conditional `replaced_by`. `inference_rationale` is required whenever canonical identity, type, label, merge/split, or authority matching is inferred and must cite source-stated evidence.

A repeating `entity_decision` instrument records `edc_id` (`EDC-00001`), controlled `edc_decision_type` (`create_entity`, `link_mention`, `preferred_label`, `entity_type`, `authority_match`, `merge`, `split`, `status_change`, `other`), conditional `edc_men_fk`, required valid JSON `edc_value_json`, JSON `edc_evidence_pas_ids`, `epistemic_status`, conditional `normalization_basis`, conditional `inference_rationale`, `certainty`, conditional `uncertainty_reason`, and the full `edc_*` creator/time/version/supersedes/status/comparison block. `edc_source_doc_fk` identifies the Project A context whose RUN/COD/PAS foreign keys must resolve. Competing human/model proposals remain separate EDC instances; only a reviewed/adjudicated decision may update the canonical entity snapshot.

A repeating `entity_variant` instrument retains exact `surface_form`, `source_document_fk`, `source_mention_fk`, and note. Merge/split operations never delete source records, entity decisions, variants, or mentions.

## Project C: canonical source registry

One Project C record is one canonical source witness. Primary `source_id` matches `^SRC-[0-9]{5}$`. The non-repeating `source` instrument contains `repository_name` (required), optional `collection_name`, required `catalog_ref`, optional supplied `surface_label`, stable `source_url` (never signed/temporary), controlled `asset_ref`, optional 64-lowercase-hex `asset_sha256` for an authorized working copy, controlled `rights_status`, required `rights_access_note`, `image_quality` (`good`, `mixed`, `poor`, `unusable`, `not_assessed`), and `source_notes`.

Only source-registry stewards and the minimum-privilege resolver service may write Project C. Project A stores only `dsr_source_fk` plus document-local relationship/order/boundary fields. Removing a DSR link never deletes its canonical SRC, and a SRC linked from any DSR cannot be deleted.

## Permissions

- **Coder:** assigned records and own incomplete instances; no export, deletion, registry merge, or unlocking.
- **Auditor/adjudicator:** validation/adjudication instruments and record locking; cannot alter submitted claims/transcriptions.
- **Data steward:** stable-ID assignment and locked IDM approval, entity/source registry reconciliation, DSR links, imports/exports, and Data Quality reports.
- **Principal investigator:** production/release and rights exceptions.
- **API service:** minimum required cross-project lookup/write fields only; separate credential per project and no restricted-binary export.

Use Data Access Groups when independent coders must be isolated. Export pseudonymous coder IDs; do not export account emails.

## Data Quality and release gate

Saved rules plus the external validator must check:

1. five-digit ID syntax and global uniqueness in and across the three projects as applicable;
2. every foreign key and target-type prefix, including cross-project SRC/ENT and Project B entity-decision RUN/COD/PAS links;
3. one immutable IDM per `(RUN, local type, local ID)`, no conflicting/reused assignment, local/stable type and prefix agreement, and valid RUN/DOC/prompt/contract/steward-COD/target references;
4. every DSR points to one canonical Project C source, no source metadata is duplicated in Project A, `(DOC,SRC)` and source order are unique, and shared-source boundary notes are complete;
5. every transcription names only in-document DSRs and every passage DSR is included in its transcription; locator SRC prefix equals the DSR's canonical SRC;
6. passage line existence, source/transcription agreement, and exact quote match;
7. exactly one durable creator route plus originating `created_at` for every PAS/MEN/ROL/EVT/EVP/REL/SCL/CLM/DEP/EDC/VAL, with valid creator operation/context;
8. valid acyclic append-only version/supersedes chains, immutable origin/version fields, monotonic statuses, distinct stable IDs for corrections, and comparison keys rather than supersession between parallel human/model alternatives;
9. normalization basis for every normalized value and source-grounded `inference_rationale` for inferred entities, entity decisions, and analytical records;
10. uncertainty reason unless certainty is high, plus role/mention/entity/document consistency;
11. event-stage and relation-overreach rules, and every EVP resolves its EVT, passage, mention/entity, controlled event role, and same-document evidence; EVT has no embedded participant list;
12. one direct PAS per SCL, and for every CLM an acyclic, fully versioned DEP chain terminating in at least one SCL;
13. exactly one active VAL per claim/auditor/audit pass, decision-specific required evidence, valid same-claim supersession lineage, and comparison keys rather than supersession for independent human/model VALs;
14. every AIF preserves the Prompt 03 finding fields, has one audit creator route, valid target/source/locator, and a resolution note when dismissed/resolved;
15. generic adjudications preserve compared IDs/value snapshots and valid targets; claim-validation adjudications additionally reference one SCL/CLM, retain all original VALs, and record a claim audit resolution;
16. complete terminal run provenance and hashes; and
17. no archival-image binary, signed URL, credential, or withheld value in public export.

Lock submitted analytical instances, approved IDM mappings, and reviewed instruments. Corrections create new stable records linked through supersession or adjudication; never overwrite source text, ID mappings, dependencies, entity decisions, claims, validations, AIF evidence/explanations, candidate values, or adjudications. Release UTF-8 exports from all three projects plus a join manifest with schema/codebook/vocabulary/prompt/form versions, row counts, SHA-256 hashes, validator version, IDM integrity result, cross-project referential-integrity result, and unresolved limitations.
