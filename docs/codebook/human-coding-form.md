# Human Coding Form

## Historical Evidence-Chain Pilot: Salem Witch Trials

**Form version:** `SAL-HUMAN-CODING-FORM-1.0`<br>
**Codebook:** `SAL-CB-0.3`<br>
**Associated prompts:** `SAL-PROMPT-TRANSCRIPTION-1.0`,
`SAL-PROMPT-EXTRACTION-1.0`, `SAL-PROMPT-AUDIT-1.0`

This is a repeatable form specification. Sections marked **repeatable** should
be implemented as linked records or repeating instruments rather than a fixed
number of columns.

## Instructions to coders

Record what the supplied source supports, not what you know or expect about the
Salem witch trials. Preserve wording and spelling; do not silently modernize
names, dates, or legal terminology. Separate source-stated content from
normalization and inference. Bind each consequential observation to an exact
source location and record uncertainty explicitly.

In particular:

- a warrant does not prove an arrest;
- a summons or witness list does not prove testimony;
- an indictment does not prove conviction;
- a shared surname does not prove kinship;
- co-occurrence does not establish a social relationship; and
- a supernatural allegation is a claim in the record, not an independently
  established event.

Complete the form independently before consulting another coder.

---

## 1. Coding administration

| Field | Entry |
|---|---|
| Coder ID | `____________________________` |
| Coding session ID | `COD-_____` |
| Document ID | `DOC-_____` |
| Coding date | `YYYY-MM-DD` |
| Start time | `HH:MM` |
| End time | `HH:MM` |
| Codebook version | `SAL-CB-0.3` |
| Form version | `SAL-HUMAN-CODING-FORM-1.0` |
| Source files received complete? | `yes / no / unclear` |
| Previous coding consulted? | `no` for independent coding; otherwise explain |

**Administration notes:**

`___________________________________________________________________________`

## 2. Source and document record

| Field | Entry |
|---|---|
| Repository or archive | `________________________________________` |
| Collection | `________________________________________ / not supplied` |
| Catalog reference | `________________________________________` |
| Source image IDs in order | `SRC-_____; SRC-_____; …` |
| Stable source URL | `________________________________________ / not supplied` |
| Rights/access status | `public / restricted / permission_required / unknown` |
| Document boundary clear? | `yes / no / unclear` |
| Document type | Select from codebook vocabulary |
| Basis for document type | `heading / formula / catalog / inferred_form / other` |
| Document-type certainty | `high / medium / low` |
| Date as written | Exact text or `not_present / illegible` |
| Normalized date | ISO value/range or blank |
| Date normalization rule | Required if normalized |
| Place as written | Exact text or `not_present / illegible` |
| Normalized place | Optional |
| Place normalization basis | Required if normalized |

**Neutral document summary (one to three sentences):**

`___________________________________________________________________________`

**Boundary, sequence, or source-quality notes:**

`___________________________________________________________________________`

## 3. Source image and legibility assessment — repeat for each image

| Field | Entry |
|---|---|
| Source image ID | `SRC-_____` |
| Sequence number | `________` |
| Page/folio label as supplied | `____________________________` |
| Side | `recto / verso / unknown / not_applicable` |
| Completeness | `complete / cropped / fragment / missing_area / unclear` |
| Overall legibility | `high / medium / low` |
| Rotation/orientation issue | `none / present / unclear` |
| Main obstruction | `none / fading / bleed_through / fold / stain / damage / crop / other` |
| Lines or regions not assessable | `________________________________________` |

## 4. Diplomatic transcription

| Field | Entry |
|---|---|
| Transcription ID | `TRN-_____` |
| Transcription status | `draft / reviewed / adjudicated / superseded` |
| Convention version | `SAL-CB-0.3` or declared local convention |
| Source image IDs | `SRC-_____; SRC-_____; …` |
| Supersedes transcription ID | Blank for first version |

**Transcription:**

```text
[Mark image/page and line boundaries. Preserve spelling and uncertainty.]


```

**General transcription notes:**

`___________________________________________________________________________`

**Transcription completeness:**

- [ ] All visible text transcribed.
- [ ] Image/page boundaries marked.
- [ ] Uncertain readings marked.
- [ ] Illegible text or physical gaps marked.
- [ ] Deletions and insertions represented.
- [ ] No silent modernization introduced.
- [ ] Proper names and negations rechecked.

## 5. Evidence passages — repeatable

Create a passage before citing it from a mention, role, event, relation, or claim.

| Field | Entry |
|---|---|
| Passage ID | `PAS-_____` |
| Source image ID | `SRC-_____` |
| Page/folio and side | `________________________________________` |
| Region/column | `________________________________________` |
| Line start–end | `________________________________________` |
| Bounding box, if used | `x, y, width, height` |
| Transcription ID | `TRN-_____` |
| Verbatim evidence | `________________________________________` |
| Passage type | `body / heading / margin / endorsement / signature / other` |
| Passage legibility | `high / medium / low / not_assessable` |
| Locator note | `________________________________________` |

## 6. Entity mentions — repeatable

Code each meaningful textual mention. Do not force identity resolution.

| Field | Entry |
|---|---|
| Mention ID | `MEN-_____` |
| Evidence passage ID | `PAS-_____` |
| Surface form | Exact source/transcription form |
| Entity type | `person / group / organization / court / government_body / place / document / legal_office / other / unknown` |
| Provisional entity ID | `ENT-_____` or blank if unresolved |
| Normalized label | Optional |
| Normalization basis | Required if normalized |
| Epistemic status | `source_stated / normalized / inferred` |
| Certainty | `high / medium / low` |
| Uncertainty reason | Required unless certainty is high |
| Inference rationale | Required if epistemic status is inferred |
| Notes/candidate matches | `________________________________________` |

## 7. Document participant roles — repeatable

Select the role performed in this document. One mention may have multiple role
records when the text supports them.

| Field | Entry |
|---|---|
| Role record ID | `ROL-_____` |
| Mention ID | `MEN-_____` |
| Role | Select from codebook section 9 |
| Evidence passage ID | `PAS-_____` |
| Epistemic status | `source_stated / normalized / inferred` |
| Certainty | `high / medium / low` |
| Uncertainty reason | Required unless high |
| Notes | `________________________________________` |

**Role safeguards:**

- [ ] `witness` is supported by witnessing/testimony language, not mere listing.
- [ ] `accused_person` is attributed to the document; allegation is not treated as fact.
- [ ] Office or legal role is supported in this document or clearly marked normalized/inferred.

## 8. Events and procedural actions — repeatable

Separate an order from its reported execution and separate each procedural
stage.

| Field | Entry |
|---|---|
| Event ID | `EVT-_____` |
| Event type | Select from codebook section 10 |
| Trigger/action as written | Exact phrase where possible |
| Primary evidence passage | `PAS-_____` |
| Additional evidence passages | `________________________________________` |
| Date as written | Exact text or structured missingness |
| Normalized date | Optional |
| Place as written | Exact text or structured missingness |
| Normalized place | Optional |
| Epistemic status | `source_stated / normalized / inferred` |
| Certainty | `high / medium / low` |
| Uncertainty reason | Required unless high |
| Notes | `________________________________________` |

### 8.1 Event participants — repeat for each participant

| Field | Entry |
|---|---|
| Event participant ID | `EVP-_____` |
| Event ID | `EVT-_____` |
| Mention/entity ID | `____________________________` |
| Participant role as written | `________________________________________` |
| Event participant role | `actor / target / declarant / authority / subject / other / unclear` |
| Evidence passage ID | `PAS-_____` |
| Epistemic status | `source_stated / normalized / inferred` |
| Certainty | `high / medium / low` |
| Uncertainty reason | Required unless high |
| Inference rationale | Required if inferred |

## 9. Attributed allegations or propositions — repeatable

Use this section for supernatural, criminal, behavioral, or other attributed
claims. Do not encode the proposition as an unqualified fact.

| Field | Entry |
|---|---|
| Claim in Source ID | `SCL-_____` |
| Claimant mention/entity | ID or `claimant_not_stated` |
| Target mention/entity | ID or `not_stated` |
| Proposition close to source wording | `________________________________________` |
| Evidence passage ID | `PAS-_____` |
| Attribution explicit? | `yes / no / unclear` |
| Polarity | `affirmed / negated / mixed / unclear` |
| Modality | `asserted / reported / possible / ordered / unclear` |
| Epistemic status | Usually `source_stated`; otherwise explain |
| Certainty of coding | `high / medium / low` |
| Notes | `________________________________________` |

## 10. Relations — repeatable

| Field | Entry |
|---|---|
| Relation ID | `REL-_____` |
| Subject ID | Mention, entity, event, or document ID |
| Predicate | Select from codebook section 11 |
| Object ID | Mention, entity, event, document, or place ID |
| Direction verified? | `yes / no / unclear` |
| Evidence passage ID | `PAS-_____` |
| Epistemic status | `source_stated / normalized / inferred` |
| Certainty | `high / medium / low` |
| Inference rationale | Required if inferred |
| Notes | `________________________________________` |

**Relation safeguards:**

- [ ] Relation is more than co-occurrence.
- [ ] Kinship is explicitly stated if `explicit_kinship` is selected.
- [ ] Shared surname was not used as sole identity or kinship evidence.
- [ ] Source and target direction matches the wording.

## 11. Normalizations and inferences review

### 11.1 Normalization log — repeatable

| Field | Entry |
|---|---|
| Related record ID | `____________________________` |
| Field normalized | `name / date / place / document_type / role / event / other` |
| Source form | `________________________________________` |
| Normalized form | `________________________________________` |
| Rule or authority | `________________________________________` |
| Ambiguous alternatives retained? | `yes / no / not_applicable` |
| Certainty | `high / medium / low` |

### 11.2 Inference log — repeatable

| Field | Entry |
|---|---|
| Related record ID | `____________________________` |
| Inferred proposition/value | `________________________________________` |
| Supporting passage IDs | `________________________________________` |
| Rationale | `________________________________________` |
| Plausible alternative | `________________________________________ / none identified` |
| Certainty | `high / medium / low` |

## 12. Claim audit — repeatable

Complete this section when assigned an extraction or claim set to audit. Audit
the supplied source support, not general historical truth.

| Field | Entry |
|---|---|
| Validation/audit ID | `VAL-_____` |
| Claim ID | `SCL-_____` or `CLM-_____`, as assigned |
| Claim text as audited | `________________________________________` |
| Cited passage IDs | `________________________________________` |
| Evidence independently located? | `yes / no / not_assessable` |
| Audit decision | `supported / partially_supported / unsupported / contradicted / not_assessable` |
| Issue type(s) | Select from codebook section 14.2 |
| Explanation | Required unless fully supported |
| Contradicting passage IDs | Required if contradicted |
| Minimally supported revision | `________________________________________` |
| Audit certainty | `high / medium / low` |
| Adjudication needed? | `yes / no` |

### 12.1 Audit integrity findings — repeatable

Create a separate record for each evidence-chain integrity failure. Do not use
an integrity finding in place of the claim's support decision.

| Field | Entry |
|---|---|
| Audit integrity finding ID | `AIF-_____` |
| Finding type | `wrong_source / broken_locator / quote_mismatch / invalid_reference / versioning_error / vocabulary_error / other` |
| Target record ID | `________________________________________` |
| Source image ID | `SRC-_____ / not_applicable` |
| Line start–end | `________________________________________ / not_applicable` |
| Minimal evidence | `________________________________________` |
| Explanation | Required |
| Human review needed? | `yes / no` |

## 13. Document-level completion review

### 13.1 Completeness

- [ ] All supplied images reviewed in order.
- [ ] Document type and basis recorded.
- [ ] Transcription completed or unassessable regions identified.
- [ ] Every consequential observation has an evidence passage.
- [ ] Source forms retained beside normalizations.
- [ ] Every inference has a rationale.
- [ ] Every medium/low-certainty value has an uncertainty reason.
- [ ] Missingness distinguishes absent, illegible, unclear, and not checked.
- [ ] Allegations are attributed rather than asserted as facts.
- [ ] Procedural stages are not conflated.
- [ ] No outside knowledge was used to fill a source gap.

### 13.2 Overall assessment

| Field | Entry |
|---|---|
| Coding complete? | `yes / no / complete_with_unresolved_items` |
| Overall document legibility | `high / medium / low` |
| Overall coding confidence | `high / medium / low` |
| Number of unresolved readings | `________` |
| Number of unresolved identities | `________` |
| Number of inferred records | `________` |
| Requires specialist review? | `yes / no` |
| Suggested specialist | `paleography / legal_history / metadata / other` |

**Most consequential unresolved issue:**

`___________________________________________________________________________`

**Final coder notes:**

`___________________________________________________________________________`

## 14. Reconciliation and adjudication

This section is completed only after independent coding has been frozen.

| Field | Entry |
|---|---|
| Comparison pair/session IDs | `________________________________________` |
| Adjudicator ID | `________________________________________` |
| Adjudication date | `YYYY-MM-DD` |
| Original records preserved? | `yes / no` |
| Codebook clarification needed? | `yes / no` |

### Disagreement record — repeatable

| Field | Entry |
|---|---|
| Adjudication ID | `ADJ-_____` |
| Affected record/field | `________________________________________` |
| Coder A value | `________________________________________` |
| Coder B value | `________________________________________` |
| Reason category | Select from codebook section 15 |
| Adjudicated value | `________________________________________ / unresolved` |
| Rationale and evidence passages | `________________________________________` |
| Requires recoding elsewhere? | `yes / no / unknown` |

## 15. Coder attestation

I completed the initial form independently, distinguished source content from
normalization and inference, and recorded material uncertainty rather than
silently resolving it.

**Coder ID:** `____________________________`<br>
**Completion date:** `YYYY-MM-DD`<br>
**Electronic acknowledgement:** `____________________________`
