# Candidate GitHub Issues

These issue briefs are ready to copy into GitHub. IDs are roadmap references, not GitHub issue numbers, and all items start as `proposed`.

## R01 — Reconcile protocol version references

**Labels:** `documentation`, `protocol`, `priority:high`
**Depends on:** none

Compare the codebook, human coding form, prompts, vocabulary, and schemas. Resolve field-name or definition drift without changing the meaning of an existing controlled code.

**Acceptance criteria:** a version matrix exists; every cross-reference resolves; changes have migration notes; two reviewers approve the reconciled set.

## R02 — Record a repository license decision

**Labels:** `governance`, `priority:high`
**Depends on:** rights review

Replace any license placeholder only after deciding whether code, documentation, transcription data, and image derivatives require different licensing terms.

**Acceptance criteria:** decision owner and rationale are recorded; LICENSE and README agree; restricted archival assets are explicitly excluded from any blanket grant.

## R03 — Freeze `SAL-CV-1.0` for the pilot

**Labels:** `metadata`, `protocol`
**Depends on:** R01

Review vocabulary coverage against the codebook and training fixtures, including uncertain/other distinctions and supernatural-claim framing.

**Acceptance criteria:** every value has a definition; no code is repurposed; mappings to both platform schemas are documented; vocabulary file parses as YAML.

## R04 — Build synthetic conformance fixtures

**Labels:** `testing`, `data`
**Depends on:** R01, R03

Create synthetic page/transcription/extraction/audit packages covering line locators, revisions, negation, reported speech, unresolved identity, and the five prohibited inference patterns.

**Acceptance criteria:** fixtures are clearly labeled synthetic; each invalid fixture names the expected error; no archival image or copied restricted text is included.

## R05 — Prototype the Airtable base

**Labels:** `airtable`, `implementation`
**Depends on:** R03, R04

Implement `SAL-AIRTABLE-1.0` in a non-production base with tables, controlled selects, validation views, role-specific interfaces, and safe export.

**Acceptance criteria:** valid fixture round-trip succeeds; all invalid fixtures enter a DQ view or fail external validation; restricted attachments cannot reach release export.

## R06 — Prototype the REDCap projects

**Labels:** `redcap`, `implementation`
**Depends on:** R03, R04

Create document-coding and entity-registry development projects, configure repeating instruments and roles, and export a reviewed data dictionary.

**Acceptance criteria:** foreign-key validator detects cross-instance errors; record locking/version workflow is demonstrated; API service account has minimum privileges.

## R07 — Implement the release validator

**Labels:** `validation`, `engineering`, `priority:high`
**Depends on:** R03, R04

Validate IDs, references, passage quotes, version chains, controlled values, claim framing, open audit findings, hashes, and prohibited release content for either platform export.

**Acceptance criteria:** deterministic command and versioned report; nonzero failure on every invalid fixture; manifest includes row counts, versions, and SHA-256 hashes.

## R08 — Choose the pilot system of record

**Labels:** `decision`, `governance`
**Depends on:** R05, R06, R07

Compare coder workflow, relational integrity, auditability, institutional requirements, maintenance cost, and export reproducibility. Select one production source of truth.

**Acceptance criteria:** decision record names criteria, evidence, owner, date, and migration path; losing prototype remains clearly non-production.

## R09 — Finalize coder training and adjudication

**Labels:** `methods`, `training`
**Depends on:** R01, R04

Prepare independent-coding exercise, ambiguity escalation, adjudication roles, and rules for consulting external sources.

**Acceptance criteria:** coder acknowledgment is recorded; consultation occurs only after independent completion; consequential decisions retain evidence and rationale.

## R10 — Predeclare reliability and evaluation plan

**Labels:** `methods`, `analysis`, `priority:high`
**Depends on:** R09

Define sampling, units, primary measures, missing/unresolved handling, confidence intervals, exclusions, and stopping/amendment rules before examining pilot results.

**Acceptance criteria:** analysis specification is versioned and dated; synthetic tests cover edge cases; post hoc analyses must be labeled exploratory.

## R11 — Create archival rights matrix

**Labels:** `rights`, `data-governance`, `priority:high`
**Depends on:** none

Record repository terms, permissions, allowed transformations, access tier, retention, and release eligibility for each proposed source set.

**Acceptance criteria:** each pilot page has an owner-reviewed status; permission evidence is retained outside the public repository where necessary; unclear items are excluded from public release.

## R12 — Run the release checklist

**Labels:** `release`, `reproducibility`
**Depends on:** R07, R08, R10, R11

Generate the allowed artifact package and verify it in a clean environment.

**Acceptance criteria:** validator passes; hashes reproduce; prohibited assets, platform IDs, emails, tokens, and signed URLs are absent; limitations and unresolved findings are published.
