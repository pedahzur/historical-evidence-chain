# Salem Historical Evidence Chain Codebook

**Version:** `SAL-CB-0.3`<br>
**Status:** Pilot working version<br>
**Scope:** Seventeenth-century legal and related records associated with the
Salem witch trials

## 1. Purpose

This codebook governs diplomatic transcription, source-bound extraction,
and claim audit. Its purpose is to preserve an inspectable chain from document
image through a Claim in Source to a downstream Historical Claim. Coders record
what the supplied source supports, not what is merely plausible from outside
knowledge.

## 2. Non-negotiable rules

1. Preserve source wording and spelling in every source-form field.
2. Never silently modernize a name, date, spelling, or legal term.
3. Keep source-stated information, normalization, and inference separate.
4. Bind every consequential observation to an exact source location.
5. Record ambiguity; do not force a decision to complete a field.
6. Code a participant's role in this document, not a permanent identity.
7. Do not infer arrest from a warrant, testimony from a summons or witness
   list, or conviction from an indictment.
8. Do not infer kinship from a shared surname.
9. Do not treat co-occurrence as a social relationship.
10. Treat supernatural allegations as claims in a historical record, not as
    independently established events.
11. Do not repair source text from memory or an external edition unless the
    protocol explicitly supplies that edition as a separate witness.
12. Complete initial coding independently before reconciliation.

## 3. Epistemic status

Every extracted observation receives exactly one status.

| Value | Definition | Example |
|---|---|---|
| `source_stated` | Explicitly expressed in the supplied document | The text calls a person a constable |
| `normalized` | A declared transformation of source-stated content | `2d mo. 1692` mapped to an ISO date under a stated calendar rule |
| `inferred` | A reasoned conclusion not explicitly stated | A damaged heading provisionally classified as a warrant |

An inferred value must have a source-stated basis, a written rationale, and a
certainty value. Normalization must retain both the original and normalized
forms and identify the rule used.

## 4. Core units and identifiers

Identifiers are stable text values and must not be replaced by database record
IDs. The five-digit patterns below are authoritative for version 0.3; a future
change requires a versioned migration rather than silent repadding.

| Unit | Identifier pattern | Definition |
|---|---|---|
| Source image | `SRC-00001` | One manuscript image, scan, frame, printed page, or other source surface |
| Document | `DOC-00001` | One archival document or explicitly defined document unit |
| Document–source junction | `DSR-00001` | One document-local use and ordering of one canonical source witness |
| Transcription | `TRN-00001` | One immutable transcription version |
| Passage | `PAS-00001` | A located segment of a source or transcription |
| Mention | `MEN-00001` | One textual mention of an entity |
| Entity | `ENT-00001` | One provisionally resolved entity; may remain unresolved |
| Entity decision | `EDC-00001` | One append-only proposal or decision about entity creation, linkage, labeling, type, authority match, merge, split, or status |
| Participant role | `ROL-00001` | One document-specific role assignment |
| Event | `EVT-00001` | One source-stated or declared inferred event |
| Event participant | `EVP-00001` | One entity or mention participating in one event in a controlled event role |
| Relation | `REL-00001` | One relation between two coded objects |
| Claim in Source | `SCL-00001` | One attributed proposition recorded in and bound directly to a source passage |
| Historical Claim | `CLM-00001` | One downstream analytical proposition submitted to audit |
| Claim dependency | `DEP-00001` | One declared support, qualification, contradiction, or transformation link for a Historical Claim |
| Validation or audit | `VAL-00001` | One immutable validation decision |
| Audit integrity finding | `AIF-00001` | One evidence-chain integrity problem found during an audit |
| Adjudication | `ADJ-00001` | One preserved resolution of a coding disagreement |
| Coding session | `COD-00001` | One coder's work session on a document |
| Model run | `RUN-00001` | One reproducible prompt execution |
| Ingest ID Map | `IDM-00001` | One retained mapping from a prompt-local identifier to its assigned stable project identifier |

Every prompt-local identifier must be resolved through an immutable `IDM`
record at controlled ingest. The record preserves the originating `RUN` and
`DOC`, prompt and contract versions, local identifier and declared type, stable
identifier and declared type, UTC mapping time, and steward `COD`. The tuple
`(run, local_type, local_id)` is unique; declared types must agree and the stable
prefix must match. A mapping is never overwritten or reused: later analytical
corrections follow the stable target's supersession chain while the `IDM`
continues to identify the originally ingested record.

Do not merge two documents solely because they appear in one image, or split a
single document solely because it occupies multiple images. Record uncertainty
about document boundaries in notes and preserve image order.

## 5. Source and document fields

| Field | Required | Rule |
|---|---:|---|
| `document_id` | Yes | Stable project identifier |
| `repository_name` | Yes | Archive or holding institution as supplied |
| `collection_name` | If known | Preserve official collection title |
| `catalog_reference` | Yes | Shelfmark, call number, or catalog identifier |
| `source_url` | If permitted | Stable archive or catalog URL, not a temporary image URL |
| `document_source_ids` | Yes | Ordered `DSR` junctions linking the document to canonical source witnesses |
| `image_quality` | Yes | `good`, `mixed`, `poor`, `unusable`, or `not_assessed` for each canonical source witness |
| `rights_access_note` | Yes | Public, restricted, permission required, or unknown |
| `document_type` | Yes | Controlled value below; `unknown` is valid |
| `document_type_basis` | Yes | Heading, formula, catalog description, inferred form, or other |
| `date_as_written` | If present | Exact source expression |
| `date_normalized` | If justified | ISO 8601 or explicit interval; never replaces source form |
| `place_as_written` | If present | Exact source expression |
| `summary` | Optional | Neutral description, not an interpretive conclusion |
| `boundary_note` | If needed | Documents/image segmentation uncertainty |

### 5.1 Document type vocabulary

Use the most specific supported value:

`complaint`, `warrant`, `warrant_return`, `examination`, `deposition`,
`testimony`, `summons`, `indictment`, `recognizance`, `petition`, `mittimus`,
`court_order`, `court_record`, `jury_verdict`, `execution_record`,
`correspondence`, `account`, `copy_or_extract`, `composite`, `other`, `unknown`.

Do not classify a procedural outcome from document type alone. A warrant may
order an arrest without proving that the arrest occurred; an indictment records
a charge without proving guilt or conviction.

### 5.2 Document–source junctions

Use one `DSR` record for each document-local use of a canonical `SRC` witness.
The junction stores `document_source_id`, `document_id`, `source_id`, source
order, an optional document-local surface label, and a `boundary_basis` of
`catalog_metadata`, `physical_continuity`, `textual_continuity`,
`editorial_decision`, `unclear`, or `other`. A boundary note is required for
`unclear` or `other` and whenever one source is linked to multiple documents.
Canonical repository, catalog, rights, URL, and asset metadata remain on `SRC`
and are not copied into `DSR`. The same source witness may be linked to more than
one document when each boundary decision is explicit.

## 6. Diplomatic transcription

### 6.1 General policy

Retain original spelling, capitalization where discernible, punctuation,
abbreviations, and meaningful line breaks. Do not modernize silently. Record
page or image boundaries. If the project expands an abbreviation, preserve the
visible form and put editorial expansion in brackets according to the adopted
convention.

### 6.2 Markup conventions

| Markup | Meaning |
|---|---|
| `[illegible]` | Text is present but cannot be read |
| `[illegible: 2 words]` | Estimated extent is known |
| `[word?]` | Uncertain reading; best candidate shown |
| `[word/ward]` | Two live alternatives, neither preferred |
| `⟦deleted text⟧` | Legible deletion or cancellation |
| `〈inserted text〉` | Interlinear or marginal insertion, with position noted |
| `[gap]` | Physical loss or missing image area |
| `[…]` | Deliberate omission only in a quoted excerpt, never a full transcription |

If these characters are impractical in an implementation, a lossless TEI-like
or structured equivalent may be used. The project must declare one convention
before coding and apply it consistently.

### 6.3 Transcription fields

Record `transcription_id`, `document_id`, `version`, `status`, `transcriber_id`,
`created_at`, ordered `document_source_ids`, `convention_version`, `transcription_text`,
and `general_notes`. `status` is one of `draft`, `reviewed`, `adjudicated`, or
`superseded`. Never overwrite a reviewed version; create a successor and link
it with `supersedes_transcription_id`.

Record `creator_type` as `human`, `model`, or `hybrid`. A human version links a
coding record, a model version links a model run, and a hybrid version retains
both creator routes. Human review of an immutable model version does not by
itself make that version hybrid; a substantive hybrid correction is a new
version.

## 7. Source location and evidence passages

Each consequential observation must cite at least one evidence passage. A valid locator
contains:

- `source_id` and any page/folio identifier;
- region or line range using the project's declared coordinate system;
- verbatim evidence text where legible; and
- transcription version used.

Preferred locator order is image/folio, side (`recto`, `verso`, or `unknown`),
column or region, and line range. Bounding boxes may supplement line numbers.
Use a document-level locator only when the proposition is genuinely established
by the document as a whole; explain why no narrower passage is possible.

Classify each passage as `body`, `heading`, `margin`, `endorsement`,
`signature`, or `other`. Use `other` only with a note describing the passage
type.

Evidence quotes must not be silently corrected to match normalized fields. An
illegible passage can support a low-certainty observation only when its readable
context justifies the proposed value. Use `unclear` when text is readable but
its classification cannot be resolved; do not invent a separate certainty code.

## 8. Entities and mentions

### 8.1 Entity type vocabulary

`person`, `group`, `organization`, `court`, `government_body`, `place`,
`document`, `legal_office`, `other`, `unknown`.

### 8.2 Mention fields

| Field | Required | Rule |
|---|---:|---|
| `mention_id` | Yes | One textual occurrence or coordinated expression |
| `passage_id` | Yes | Exact evidence location |
| `surface_form` | Yes | Text exactly as transcribed |
| `entity_type` | Yes | Controlled value |
| `entity_id` | Optional | Leave blank when identity cannot be resolved |
| `normalized_label` | Optional | Declared normalization; never replaces surface form |
| `normalization_basis` | If normalized | Rule, authority file, or internal cross-reference |
| `epistemic_status` | Yes | Normally `source_stated`; declare any normalization or inference |
| `certainty` | Yes | Controlled value in section 12 |
| `uncertainty_reason` | If not high | Explain uncertainty |
| `inference_rationale` | If inferred | Source-stated basis and reasoning |

### 8.3 Identity resolution

Shared names or surnames do not establish identity. Link mentions only when the
document or declared authority evidence supports the match. Record possible
matches as candidates, not as merged entities. Later entity merges must be
reversible and preserve the original mention coding.

### 8.4 Entity decisions

Record each identity-resolution proposal or resolution as an append-only `EDC`
record. Controlled decision types are `create_entity`, `link_mention`,
`preferred_label`, `entity_type`, `authority_match`, `merge`, `split`,
`status_change`, and `other`. Each decision retains its creator route, evidence,
epistemic status, certainty, rationale when inferred, version, and comparison
key. Independent human and model decisions coexist until review or
adjudication; updating the canonical `ENT` snapshot never erases the `EDC`
history or original mentions.

## 9. Participant roles

Roles are document-specific and may be multiple. Use:

`author_or_creator`, `scribe`, `signatory`, `addressee`, `complainant`,
`accused_person`, `deponent`, `examinee`, `examiner`, `witness`, `juror`,
`judge_or_magistrate`, `clerk`, `constable`, `marshal`, `sheriff`, `jailer`,
`minister`, `physician`, `interpreter`, `surety`, `petitioner`, `named_third_party`,
`victim_or_afflicted_person`, `official_other`, `other`, `unclear`.

The label `victim_or_afflicted_person` records the role or language used in the
historical legal record; it does not validate a supernatural explanation.
`Witness` requires evidence of witnessing or giving testimony in this document;
appearance in a list is insufficient unless the list explicitly identifies
witnesses.

Role records include `role_id`, `mention_id`, `document_id`, `role`, `evidence_passage_id`,
`epistemic_status`, `certainty`, and notes.

## 10. Events and procedural actions

### 10.1 Event type vocabulary

`allegation_made`, `complaint_filed`, `warrant_issued`, `summons_issued`,
`service_or_execution_reported`, `arrest_reported`, `custody_or_commitment`,
`examination_held`, `statement_or_deposition_given`, `indictment_returned`,
`arraignment_or_plea`, `trial_proceeding`, `verdict_reported`,
`sentence_reported`, `release_or_bail`, `petition_submitted`, `death_reported`,
`other_procedural_action`, `other`, `unknown`.

### 10.2 Event fields

Record `event_id`, `event_type`, `trigger_as_written`, `evidence_passage_id`,
linked `EVP` records, `date_as_written`, `date_normalized`,
`place_as_written`, normalized place if used, `epistemic_status`, `certainty`,
and notes.

An order and its execution are separate events. Code `warrant_issued` when a
warrant is supported; code `arrest_reported` only when the document reports an
arrest or a return supports execution. Likewise, an allegation is not a finding,
an examination is not a confession, an indictment is not a conviction, and a
sentence is not proof that it was carried out.

### 10.3 Event participants

Create one `EVP` record for each source-supported participation in an event.
Use exactly one controlled `event_participant_role` per record; create separate
records when one entity performs more than one supported role:

- `actor`: performs the event action;
- `target`: is the person, group, place, or object toward which the action is
  directed;
- `declarant`: makes the statement, deposition, complaint, allegation, or
  other declaration represented by the event;
- `authority`: issues, authorizes, or presides over the official action;
- `subject`: is centrally involved when the preceding roles do not accurately
  describe the source-stated participation;
- `other`: another supported event role, with a note; or
- `unclear`: participation is supported but its event role cannot be resolved.

Each record includes `event_participant_id`, `event_id`, `passage_id`,
`mention_id` when available, `entity_id`, `event_participant_role`,
`role_as_written`, `epistemic_status`, `certainty`, and any required
`uncertainty_reasons` or `inference_rationale`. An `EVP` role describes
participation in one event; it does not replace the document-specific `ROL`
record.

## 11. Relations and claims

### 11.1 Relation vocabulary

`named_in`, `participates_in`, `authored_or_signed`, `addressed_to`,
`accuses_or_alleges_against`, `testifies_or_states_about`, `orders_action_by`,
`action_directed_at`, `held_office`, `located_at`, `explicit_kinship`,
`explicit_association`, `document_copy_of`, `document_part_of`, `other`.

Every relation identifies a subject, predicate, object, evidence passage,
epistemic status, certainty, and direction. Direction is `directed` when the
source supports an ordered subject-to-object relation and `undirected` when the
relation is symmetric or no ordered direction is supported. Use
`explicit_kinship` only when the source states kinship. Do not convert
co-mention, adjacency, or a shared surname into a relation.

### 11.2 Claim in Source records

A Claim in Source (`SCL`) is one proposition bound directly to one passage.
Represent an allegation as an attributed Claim in Source with a claimant when
stated, a target when stated, the proposition as close to source wording as
possible, and an evidence passage. Do not encode the alleged supernatural act
as an unqualified historical event. Where no claimant is named, use an explicit
`claimant_not_stated` flag rather than inventing one.

Record polarity as `affirmed`, `negated`, `mixed`, or `unclear`, and modality as
`asserted`, `reported`, `possible`, `ordered`, or `unclear`. Preserve the
document's attribution: `reported` describes a proposition presented as
someone's statement or account, while `ordered` describes a direction or
obligation and does not establish completion.

### 11.3 Historical Claims and dependencies

A Historical Claim (`CLM`) is a downstream analytical proposition. It is not a
direct quotation and does not cite a passage as a substitute for its analytical
dependencies. Each `CLM` declares one or more `DEP` records linking it to a
supporting `SCL` or another `CLM`; every dependency chain must terminate in at
least one passage-bound `SCL`.

Use dependency type `supports`, `qualifies`, `contradicts`, `normalization`,
`identity_resolution`, or `procedural_sequence`. Each dependency records the
dependent Historical Claim, exactly one supporting claim, and a rationale.
Self-links and cycles are invalid.

## 12. Certainty and missingness

### 12.1 Certainty

| Value | Meaning |
|---|---|
| `high` | Clear reading and classification; no material alternative |
| `medium` | Preferred reading/classification but a credible alternative remains |
| `low` | Tentative proposal; evidence is weak, damaged, or highly ambiguous |
| `not_assessed` | Certainty was not assessed; allowed only in imported legacy data |

Certainty concerns the coding decision, not whether a historical allegation was
true.

### 12.2 Missingness

Use structured missingness rather than an empty string when the distinction
matters:

- `not_present`: the document does not state the value;
- `illegible`: content is present but unreadable;
- `unclear`: content can be read but its classification is unresolved;
- `not_applicable`: the field does not apply;
- `not_checked`: coding is incomplete;
- `withheld`: value is not released because of access or rights restrictions.

Never use `not_present` to mean `not_checked`.

## 13. Normalizing dates, names, and places

Normalization is optional and conservative.

- Preserve the date exactly as written.
- State the calendar and year-start convention used for every normalized date.
- Represent partial dates and ranges without false precision.
- Preserve title, abbreviation, and spelling in a person's surface form.
- Do not expand initials or resolve aliases from general knowledge.
- Preserve place wording; link to an authority only when the match is
  sufficiently supported and the authority source is recorded.

Where a conversion is disputed, retain multiple candidates with rationales or
leave the normalized field blank.

## 14. Claim audit

The audit unit is one explicit proposition: either a source-bound Claim in
Source or a downstream Historical Claim. Decompose compound claims when their
parts may receive different decisions. For a Historical Claim, follow every
declared dependency until the chain reaches passage-bound Claim in Source records;
prior validation of an upstream record does not automatically validate the
downstream proposition.

### 14.1 Audit decisions

| Value | Definition |
|---|---|
| `supported` | The cited source directly supports the full claim |
| `partially_supported` | The source supports only part of the claim or supports a weaker formulation |
| `unsupported` | The supplied source does not provide adequate support |
| `contradicted` | The supplied source provides evidence inconsistent with the claim |
| `not_assessable` | Damage, missing material, or insufficient context prevents a decision |

### 14.2 Audit issue types

Use one or more of: `incorrect_transcription`, `wrong_span`, `missing_span`,
`entity_misidentification`, `role_overreach`, `event_overreach`,
`relation_overreach`, `normalization_error`, `date_error`, `negation_error`,
`modality_error`, `attribution_error`, `procedural_stage_conflation`,
`external_knowledge_leakage`, `compound_claim`, `insufficient_context`, `other`.

An audit record includes claim text, cited passages, decision, issue types,
explanation, proposed minimally supported revision, auditor, timestamp,
certainty, and any need for adjudication. The auditor must not silently edit the
original claim.

### 14.3 Audit integrity findings

Use a separate `AIF` record for an evidence-chain integrity problem that is not
itself the support decision on a claim. Controlled `finding_type` values are:

- `wrong_source`: the referenced source witness is not the required witness;
- `broken_locator`: a source, page, region, or line locator does not resolve;
- `quote_mismatch`: the exact quote does not match the cited immutable
  transcription span;
- `invalid_reference`: an identifier is missing, has the wrong object type, or
  does not resolve;
- `versioning_error`: a record cites the wrong version or violates append-only
  or supersession rules;
- `vocabulary_error`: a controlled value is unavailable or misapplied; or
- `other`: another documented integrity failure, with an explanation.

Each finding records `audit_integrity_finding_id`, `finding_type`, `target_id`,
source and line locators when applicable, minimal evidence, an explanation, and
whether human review is required.

## 15. Quality control and adjudication

1. Coders complete training documents and discuss the codebook, not the
   evaluation documents.
2. Two coders independently code each evaluation document.
3. Automated validation checks required fields, identifier uniqueness, allowed
   values, broken links, and evidence coverage.
4. Disagreements are logged before discussion.
5. An adjudicator records a resolution and rationale in a separate record.
6. Original coder records remain immutable.
7. Recurring ambiguities produce a versioned codebook clarification applied
   prospectively or through a documented recoding decision.

Disagreement reasons are: `visual_reading`, `segmentation`, `entity_boundary`,
`identity_resolution`, `ontology_boundary`, `role_classification`,
`normalization`, `inference`, `provenance`, `procedural_interpretation`, or
`other`.

## 16. Minimum validation rules

- All project identifiers are unique and match their declared pattern.
- Every observation links to an existing document and transcription version.
- Every document–source junction links exactly one document to one canonical
  source witness and has a unique order within that document.
- Every consequential source-stated, normalized, or inferred value has an
  evidence passage.
- Every normalization has a source form and normalization basis.
- Every inference has a rationale and certainty.
- Every value below `high` certainty has an uncertainty reason.
- Every event participant and relation endpoint resolves to an existing coded
  object or an explicit unresolved placeholder.
- Every event participant has one controlled event-participant role and a
  passage that supports the participation.
- Every entity change is backed by a preserved entity-decision record; inferred
  decisions cite source-stated evidence and include a rationale.
- Every Historical Claim has a declared dependency chain that terminates in at
  least one passage-bound Claim in Source.
- A superseded record links to its successor or predecessor; it is never
  deleted from the audit trail.
- Audit decisions other than `supported` include an explanation.
- `contradicted` decisions cite contradicting evidence.
- Every audit integrity finding has a controlled finding type, resolvable
  target, explanation, and source locator when the finding concerns evidence.

## 17. Change control

Codebook changes require a new version, date, author, reason, affected fields,
and a statement about whether existing records must be recoded. Controlled
vocabulary labels must not be repurposed. Deprecate a value and map it to a new
one instead of changing its meaning in place.
