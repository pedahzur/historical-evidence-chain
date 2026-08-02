# Cross-Platform Schema Notes

**Notes ID:** `SAL-SCHEMA-NOTES-1.0`
**Applies to:** `SAL-AIRTABLE-1.0`, `SAL-REDCAP-1.0`
**Codebook:** `SAL-CB-0.3`
**Vocabulary:** `SAL-CV-1.0`

## Authoritative identifier family

The implementation uses the five-digit Airtable-family IDs below. These are stable text values, never platform row IDs or repeat-instance numbers.

| Object | Pattern |
|---|---|
| Source/image witness | `SRC-00001` |
| Document | `DOC-00001` |
| Document–source junction | `DSR-00001` |
| Transcription version | `TRN-00001` |
| Passage/evidence span | `PAS-00001` |
| Mention | `MEN-00001` |
| Entity | `ENT-00001` |
| Entity decision | `EDC-00001` |
| Role | `ROL-00001` |
| Event | `EVT-00001` |
| Event participant | `EVP-00001` |
| Relation | `REL-00001` |
| Claim in Source | `SCL-00001` |
| Model run | `RUN-00001` |
| Human coding record/session | `COD-00001` |
| Ingest ID mapping | `IDM-00001` |
| Validation/audit decision | `VAL-00001` |
| Audit integrity finding | `AIF-00001` |
| Adjudication | `ADJ-00001` |
| Historical Claim | `CLM-00001` |
| Claim dependency | `DEP-00001` |

IDs are globally unique within type, immutable, and never reused. Every prompt-local assignment (for example, `SCL-LOCAL-001` or `CLM-LOCAL-001` to a five-digit stable ID) creates and permanently retains an `IDM-00001` record at controlled ingest. Any earlier `SAL-DOC`/`SAL-IMG`/`SAL-TRX` working identifiers require an explicit migration table; they are not a second live scheme.

## Evidence-chain objects

```text
SRC source witness -> DSR document-source junction -> DOC document
DOC -> TRN immutable transcription -> PAS passage -> DSR -> SRC
PAS -> MEN -> ENT
ENT -> EDC                          (append-only identity/resolution decisions)
PAS -> ROL / EVT / REL / SCL       (source-bound observations and claims)
EVT -> EVP -> MEN / ENT; EVP -> PAS (participant role with exact evidence)
CLM -> DEP -> SCL or CLM           (declared support/dependency chain)
RUN xor COD -> each versioned analytical object / EDC / AIF
prompt-local ID + RUN + DOC + steward COD -> IDM -> originally assigned stable ID
SCL or CLM -> VAL -> optional ADJ  (per-claim audit and resolution)
any frozen record/field disagreement -> ADJ
```

`SRC` represents one canonical image/surface or other supplied source witness and holds archival locator, rights/access, and asset-integrity metadata exactly once. `DSR` is the authoritative many-to-many junction between a DOC and SRC; it carries document-local order, optional local surface label, boundary basis/note, creator route, and timestamp. A shared SRC is not cloned. In REDCap, SRC records live in the separate canonical `SAL_SOURCE_REGISTRY` project and every document stores only repeating DSR junctions; source metadata must not be duplicated into the document-coding project. `PAS` is the smallest reusable evidence anchor and names its exact transcription and DSR; the DSR resolves its canonical SRC for line locators.

## Ingest ID map

`IDM-00001` is a first-class, immutable ingest-provenance record, not a disposable import log. One IDM stores the originating RUN and DOC, exact `prompt_id` and `contract_version`, exact prompt-emitted `local_id` and declared local type, assigned five-digit `stable_id` and declared stable type, immutable UTC `mapped_at`, and the steward COD that approved the assignment.

- `(RUN, local type, local ID)` is unique. Within a run, a local key has exactly one stable assignment and a newly created stable ID cannot be assigned to two local IDs.
- Local and stable types must agree; the stable prefix must match the declared type and target table/project. RUN, DOC, prompt, contract, steward COD, and target record must resolve in the same ingest context, including cross-project ENT/SRC targets where applicable.
- An existing stable ID cited as a foreign-key reference is not remapped. IDM records only controlled assignments for prompt-local objects.
- Approved IDM rows are never overwritten, deleted, or reused. If the ingested analytical record is later corrected, IDM continues to point to the originally assigned stable record and the target's `supersedes` chain leads to the correction.
- Airtable implements an `Ingest ID Map` table; REDCap Project A implements a repeating `ingest_id_map` instrument. Public releases include IDM so local prompt output remains traceable to stable scholarly records.

## Creator provenance, comparison, and immutable versions

Every PAS, MEN, ROL, EVT, EVP, REL, SCL, CLM, DEP, EDC, and VAL version has exactly one durable origin route: a model `RUN` or human `COD`. It also has required immutable `created_at`, integer `version`, same-type `supersedes`, lifecycle `status`, and an optional immutable `comparison_key`. Platform-created timestamps/users are operational metadata and cannot replace these exported fields.

- Version 1 has no `supersedes`; a later version links exactly one earlier stable ID in the same logical lineage, increments the version, and cannot create a cycle.
- Stable ID, creator route, `created_at`, `version`, `supersedes`, and `comparison_key` never change after assignment. Substantive values are immutable from `submitted` onward. Corrections append a new stable record and preserve the predecessor.
- Status transitions are monotonic and logged; allowed analytical states are `draft`, `submitted`, `reviewed`, `audited`, `adjudicated`, `superseded`, and `withdrawn` as applicable. VAL uses its audit-specific `open`, `reviewed`, `adjudicated`, and `superseded` set.
- Independent human and model outputs are parallel records, not versions of each other. A shared `comparison_key` may align alternatives. Only a genuine correction of the same logical object uses `supersedes`.
- Adjudication may accept an alternative or create a synthesized successor with its own human COD route; it never edits the compared inputs.

Canonical ENT records are registry anchors. Append-only EDC records carry proposed `create_entity`, mention-link, preferred-label/type, authority-match, merge, split, or status decisions, with the same origin/version/comparison contract. This permits model and human identity decisions to coexist until review. Every inferred ENT value and EDC decision requires `inference_rationale` citing source-stated evidence; normalization notes alone do not satisfy that requirement.

## Epistemic and operational fields

Every `MEN`, `ROL`, `EVT`, `EVP`, `REL`, `SCL`, `CLM`, `EDC`, normalized entity value, and normalized date carries exactly one `epistemic_status`: `source_stated`, `normalized`, or `inferred`. Every model/human operation records `operation_class`: `transcription`, `extraction`, or `audit`.

- `normalized` retains source form plus `normalization_basis`; it does not overwrite the source.
- `inferred` requires cited source-stated basis, written rationale, certainty, and—when certainty is not `high`—an `uncertainty_reason`.
- Every record below `high` certainty requires one or more `uncertainty_reason` values and an optional clarifying note.
- `other` is a supported category outside a vocabulary and requires a note; it is not a synonym for `unknown`, `unclear`, or `not_checked`.

## Events, allegations, and claims

An order and its reported execution are separate events. A warrant does not establish arrest; a summons/list does not establish testimony; an indictment does not establish conviction. `EVT` records source-stated or explicitly declared inferred events with their procedural type and evidence.

Each event participant is a separate `EVP-00001` junction, never an embedded list on EVT. EVP requires one EVT, one exact PAS, a MEN when a source-form mention exists (otherwise an explained direct ENT link), exact `role_as_written` or explicit missingness, and one controlled `event_participant_role`: `actor`, `target`, `declarant`, `authority`, `subject`, `other`, or `unclear`. The PAS and EVT must belong to the same document. The general document-level `participant_role` vocabulary used by ROL is distinct from `event_participant_role`.

`SCL` is a **Claim in Source**: one proposition extracted from and bound directly to one `PAS`. An allegation, including supernatural content, is an attributed SCL rather than an unqualified event. `CLM` is a downstream **Historical Claim** that may synthesize or restate source material and therefore must declare support through `DEP`. A dependency connects one CLM to a supporting SCL or another CLM; every chain must terminate in at least one passage-bound SCL. A dependency never substitutes for source evidence. Each DEP is itself an independently attributable, versioned analytical assertion with RUN-xor-COD creator, `created_at`, version/status/supersedes, and optional comparison key; competing human/model dependency graphs are preserved rather than merged in place.

Each `VAL` is an immutable audit decision on exactly one SCL or CLM: `supported`, `partially_supported`, `unsupported`, `contradicted`, or `not_assessable`. It has exactly one auditor RUN-or-COD route, immutable `created_at`, integer version, same-claim/same-audit-assignment `supersedes`, audit-specific status, and optional comparison key for aligned human/model decisions. A corrected validation appends a new VAL and retains its predecessor as `superseded`; independent auditor decisions never supersede one another. Non-supported decisions include issue type, explanation, minimally supported revision where possible, certainty, and adjudication need. `contradicted` cites at least one contradicting `PAS`. Auditors never edit the original claim.

`AIF-00001` stores one Prompt 03 `integrity_findings[]` object separately from claim decisions. Its preserved prompt-native fields are `finding_type`, `target_id`, conditional `source_id` and line range, `evidence`, `explanation`, and `needs_human_review`; schema context adds target type, document/transcription, exactly one RUN-or-COD audit creator, `created_at`, version/supersedes, and resolution status/note. Allowed prompt finding types are `wrong_source`, `broken_locator`, `quote_mismatch`, `invalid_reference`, `versioning_error`, `vocabulary_error`, and `other`. Resolving or dismissing a finding never rewrites its original evidence or explanation.

`ADJ` is generic. It may target a whole frozen record, one named field, or a claim-validation dispute. Every ADJ preserves the affected stable target, all compared record IDs and/or frozen candidate values, disagreement reasons, evidence, resolution, rationale, and human adjudication COD route. A claim-validation ADJ additionally links the SCL/CLM and all relevant VAL records and stores one audit-decision resolution (or `unresolved`). Original records, field values, and validations remain unchanged.

## Missingness and dates

Use explicit missingness: `not_present`, `illegible`, `unclear`, `not_applicable`, `not_checked`, or `withheld`. Blank is permitted only when the field is structurally optional and the reason is unambiguous.

Preserve dates as written. A normalized date requires a basis naming the calendar and year-start rule, with partial dates/ranges represented without false precision. Do not silently convert disputed dates.

## Versioning and release integrity

Reviewed transcriptions and submitted analytical records are append-only. A transcription correction creates a new `TRN`; a PAS/MEN/ROL/EVT/EVP/REL/SCL/CLM/DEP/EDC/VAL correction creates a new stable record/version. Each links its exact predecessor through `supersedes`, and every passage remains bound to the transcription/DSR/SRC actually used. Ingest ID mappings, entity decisions, validations, integrity findings, and adjudications likewise retain their originals.

Before release, validate that all IDs and foreign keys resolve; every prompt-local assignment has one valid immutable IDM and every IDM target exists; SRC metadata exists once in the canonical registry; every DOC–SRC use passes through a valid DSR; passage quotes and locator SRCs match their transcription/DSR; all required creator routes/timestamps and immutable supersession fields are complete for DEP and VAL as well as other versioned records; normalized/inferred values meet basis/rationale rules; every EVP has a controlled role and exact PAS; every claim has evidence; every claim audit is present for the audited set; every AIF is retained and properly resolved or disclosed; and generic/claim adjudications preserve all alternatives. No unresolved critical integrity error may remain.

Export UTF-8 tables from every Airtable table or all three REDCap projects plus a join manifest containing schema/codebook/vocabulary/prompt/form/contract versions, timestamps, row counts, SHA-256 hashes, IDM integrity result, cross-project referential-integrity result, validator version, and unresolved limitations.

Exclude platform row IDs, user emails, credentials, signed asset URLs, and restricted archival images. Airtable and REDCap cannot enforce every cross-record rule natively, so a versioned export validator is required.
