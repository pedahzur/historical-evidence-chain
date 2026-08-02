# Project Roadmap

**Roadmap ID:** `SAL-ROADMAP-1.0`
**Status:** proposed; no study results are asserted here.

## Milestone 0 — Repository foundation

Publish the project framing, versioned protocol materials, contribution guidance, citation metadata, and a license decision. Confirm that no restricted archival images or credentials are present in Git history.

**Exit criteria:** required files are present; internal links and version references pass review; rights scan is clean; license status is explicit.

## Milestone 1 — Protocol freeze

Reconcile `SAL-CB-0.3`, `SAL-HUMAN-CODING-FORM-1.0`, the three `SAL-PROMPT-*` files, and `SAL-CV-1.0`. Prepare a small synthetic training set that exercises unclear text, negation, document-local roles, and every prohibited inference rule.

**Exit criteria:** two reviewers approve definitions and examples; change log records remaining open questions; synthetic fixtures contain no archival image material.

## Milestone 2 — Data-system prototypes

Implement `SAL-AIRTABLE-1.0` and `SAL-REDCAP-1.0` in disposable test environments. Build import/export mappings and the shared release validator. Evaluate which platform will be authoritative for the pilot rather than maintaining two competing production sources of truth.

**Exit criteria:** round-trip fixture data retains identifiers and evidence links; all deliberately invalid fixtures are detected; platform choice and rationale are recorded.

## Milestone 3 — Coder training and reliability design

Finalize independent-coding instructions, adjudication workflow, coder blinding where required, sampling unit, and predeclared agreement measures. The statistical plan must specify how missing/unresolved codes and prevalence-sensitive categories will be treated before pilot outcomes are examined.

**Exit criteria:** training completion is documented; dry-run disagreements lead to protocol clarification rather than retroactive result tuning; reliability analysis script passes synthetic tests.

## Milestone 4 — Bounded pilot

Create a rights-cleared sampling manifest, run transcription/extraction/audit under pinned versions, and preserve human sessions and model-run provenance. Do not expand the sample or alter primary rules in response to observed results without a documented protocol amendment.

**Exit criteria:** every released record has an evidence chain; audit findings are dispositioned; deviations and exclusions are reported; no claim exceeds source support.

## Milestone 5 — Reproducible release

Produce versioned CSV/JSON exports, manifests, checksums, validation report, data dictionary, method limitations, and permitted derived materials. Deposit only artifacts authorized for redistribution.

**Exit criteria:** a clean-environment reproduction succeeds; citation metadata names the release version; rights and provenance review is signed off; unresolved limitations are visible.

## Decision gates

- **Rights gate:** before any archival asset enters a workflow or release.
- **Protocol gate:** before coding the evaluation sample.
- **Platform gate:** before production data entry.
- **Release gate:** after referential-integrity, evidence-chain, provenance, and rights checks all pass.

Candidate work items with acceptance criteria are maintained in [`issues.md`](issues.md). They are proposals, not claims that corresponding GitHub issues already exist.
