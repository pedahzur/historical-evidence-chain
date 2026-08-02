# From Page Image to Historical Claim: A Reproducible Evidence-Chain Pilot on the Salem Witch Trials

## Status of this document

Working article draft. This version specifies the research design and planned
analysis; it does **not** contain empirical findings. Source counts, archive
references, model versions, dates, and results should be added only after the
pilot is frozen and run.

## Abstract

Generative language models can transcribe, structure, and review historical
documents, but fluent outputs can obscure where a claim came from, how much
normalization occurred, and whether an inference exceeds the source. This
article proposes a small, auditable pilot using seventeenth-century records
associated with the Salem witch trials. The workflow separates three tasks:
diplomatic transcription, structured extraction, and evidence-chain
audit. Every consequential output is bound to a document and source span;
source-stated content is kept distinct from normalization and inference; and
uncertainty is represented rather than silently resolved. Source-bound
observations can be validated before they enter analysis, while downstream
Historical Claims are audited after they are formulated. Independent human
coding provides a parallel comparison condition. The study evaluates field-
level accuracy, provenance completeness, unsupported inference, uncertainty
calibration, and the kinds of disagreement that arise among models and human
coders. The intended contribution is not a definitive dataset about Salem, but
a reusable method for assessing whether AI-assisted historical research
preserves the chain of evidence from archival image to analytical claim.

## 1. Introduction

Historians routinely move through several interpretive layers: an archival
image is read as text, textual references are identified as people or events,
historical language is normalized for comparison, and observations are joined
into an argument. Each step can introduce error. In conventional scholarship,
citations, quotations, notes, and archival conventions make at least part of
that movement inspectable. Language-model workflows risk collapsing the same
steps into a single persuasive answer.

The relevant question is therefore not simply whether a model can produce a
plausible transcription or recognize a name. It is whether the resulting claim
remains traceable to the source, whether interpretive transformations are
declared, and whether unsupported conclusions can be detected before they enter
analysis. These requirements are especially important for records produced in
legal and coercive settings. Salem records document allegations, examinations,
warrants, testimony, indictments, and court actions; they do not independently
establish the supernatural claims they contain.

This pilot develops an explicit historical evidence chain. It treats
transcription, extraction, and audit as separate operations and compares model
outputs with independently completed human coding. The design is intentionally
small enough for close source review and sufficiently formal for replication.

## 2. Research questions

The pilot asks:

1. How accurately do language models produce diplomatic transcriptions of
   selected Salem legal records?
2. How reliably do models extract source-stated entities, roles, dates, places,
   events, and relationships without importing historical knowledge?
3. Does a separate audit stage identify unsupported or weakly supported Claim
   in Source records before analysis and Historical Claims after formulation?
4. Where do human coders and model runs disagree, and which disagreements are
   transcriptional, classificatory, normalizing, inferential, or evidentiary?
5. How completely does each workflow preserve provenance at the level needed
   for a second researcher to verify a claim?

The study distinguishes correctness from traceability. A historically correct
statement is still procedurally unsupported if the supplied document does not
support it.

## 3. Conceptual framework

### 3.1 The evidence chain

The project models the evidence chain as a sequence of linked objects:

`source image → source location → transcription passage → source-bound observation or Claim in Source → declared normalization/inference → source-bound validation → analysis → Historical Claim → downstream claim audit`

Validation therefore occurs at two distinct points. A source-bound observation
or Claim in Source can be checked against its passage before it is admitted to
analysis. After analysis formulates a Historical Claim, that claim receives its
own audit: the auditor follows declared dependencies through supporting Claim
in Source records (or intermediate Historical Claims) until the chain
terminates in passages. A prior validation does not automatically validate a
later claim.

No downstream object should erase its upstream form. A normalized name does
not replace the name as written; a standardized date does not replace the
document's date expression; and an inferred relationship does not become a
source-stated relationship.

### 3.2 Three epistemic layers

Each observation is assigned to one of three layers:

- **Source-stated:** explicitly expressed in the document.
- **Normalized:** a declared transformation of source-stated content, such as a
  standardized date or controlled document type.
- **Inferred:** a conclusion not explicitly stated, accompanied by a rationale
  and confidence assessment.

The distinction prevents normalization from masquerading as transcription and
historical plausibility from masquerading as documentary evidence.

### 3.3 Document-specific roles

Participants are coded according to what they do in the specific document.
The same person may be an accused person in one record, a deponent in another,
and a named third party in a third. A role is therefore attached to a mention or
document participation record, not treated as an immutable property of a
person.

## 4. Corpus and sampling

The pilot corpus will contain a purposive sample of digitized Salem-related
legal records representing several document types and levels of legibility.
Candidate types include complaints, warrants, returns, examinations,
depositions, summonses, indictments, recognizances, petitions, and court
records. The final sample should be frozen before evaluation and documented in
a manifest containing the project document identifier, archive and collection,
catalog reference, image sequence, access conditions, document type, date as
written, and selection rationale.

Sampling should vary handwriting difficulty, physical degradation, layout,
document length, number of named participants, and procedural stage. Selection
must not be based on whether a model already performs well on a document.
Restricted archival images are not redistributed. The repository contains
metadata and, where permission allows, stable links or identifiers sufficient
for an authorized researcher to locate the source.

## 5. Experimental design

### 5.1 Units of analysis

The primary units are:

- the document for overall completion and provenance;
- the located text span for transcription and evidence binding;
- the observation for entities, roles, dates, places, events, and relations;
- the claim for audit decisions; and
- the coder or model run for reproducibility and disagreement analysis.

### 5.2 Conditions

Each selected document is processed through three versioned prompts:

1. **Transcription:** produce a diplomatic transcription, marking line
   boundaries, deletions, insertions, illegible text, and uncertain readings.
2. **Extraction:** derive structured observations from the transcription while
   binding each observation to exact evidence and separating source form,
   normalization, and inference.
3. **Audit:** test each submitted Claim in Source or Historical Claim against
   its declared evidence chain, classifying support and identifying overreach,
   missing provenance, or contradictory evidence. Source-bound records may be
   audited before analysis; downstream Historical Claims are separately audited
   after formulation.

Model, provider, model snapshot, prompt version, parameters, input hashes,
timestamps, and output hashes are recorded for every run. Runs are independent;
the audit stage receives only the materials declared in the protocol.

### 5.3 Human comparison

At least two trained coders independently complete the parallel human coding
form before reconciliation. Coders receive the same source materials and
codebook but do not consult one another during initial coding. A separate
adjudicator reviews disagreements without overwriting the original records.
Training examples are excluded from the evaluation sample.

### 5.4 Preventing leakage

The extraction instruction limits coding to the supplied source. Prior
knowledge may not be used to fill gaps. Evaluation documents should be kept
separate from prompt-development examples. If external authority files are used
for a secondary normalization experiment, that condition must be labeled and
evaluated separately.

## 6. Annotation and data model

The codebook defines stable project identifiers for sources, documents,
transcriptions, passages, mentions, roles, events, relations, claims,
validations, adjudications, coding sessions, and runs. Every
consequential observation includes:

- the source document identifier;
- an exact source locator;
- a verbatim evidence quote where legible;
- source form and, when applicable, normalized form;
- epistemic status;
- certainty and uncertainty reason;
- creator and timestamp; and
- version or supersession information.

Observations are append-only. Corrections create new versions or adjudication
records so that the original decision remains inspectable.

## 7. Planned evaluation

### 7.1 Transcription

Where a reviewed reference transcription is available, the study will report
character and word error measures alongside historically meaningful error
categories: names, dates, negation, legal formulae, line omission, expansion of
abbreviations, and unmarked uncertainty. Automated distance measures will not
be treated as sufficient because several equally defensible diplomatic
readings may exist.

### 7.2 Structured observations

Entity, event, role, and relation outputs will be matched at the field level.
The analysis will report precision, recall, and F1 where a stable reference set
can be adjudicated. It will also report exact agreement for controlled fields
and separate scores for evidence-span binding and normalization. An observation
with the right label but no verifiable span will be counted as incomplete
provenance, not fully correct.

### 7.3 Audit performance

Audit decisions for both Claim in Source records and Historical Claims will use the codebook categories `supported`,
`partially_supported`, `unsupported`, `contradicted`, and `not_assessable`.
Planned measures include detection of seeded unsupported claims, false-positive
challenges to supported claims, and accuracy of stated reasons. Naturally
occurring extraction errors will be analyzed separately from deliberately
seeded audit cases.

### 7.4 Agreement and disagreement

Human-human and model-human agreement will be reported with a statistic suited
to each field and with raw agreement. More importantly, disagreements will be
classified by cause: visual reading, segmentation, ontology boundary,
normalization, inference, provenance, or procedural interpretation. This error
taxonomy is a substantive output of the pilot.

### 7.5 Uncertainty calibration

Confidence labels will be compared with adjudicated correctness. The analysis
will examine whether low-confidence outputs actually contain more errors and
whether systems appropriately abstain when the source is illegible or the
claim cannot be assessed.

The analysis plan, matching rules, exclusion criteria, and metric
implementations should be frozen before inspecting aggregate results.

## 8. Reproducibility and governance

The repository versions prompts, codebook, coding form, schemas, controlled
vocabularies, and analysis specifications. Data releases should include only
materials the project is permitted to redistribute. Checksums may identify
restricted local images without exposing them. Any public derived text must be
reviewed against archive terms and applicable law.

Human and model outputs should be distinguishable, and every correction should
retain authorship and history. The project should document conflicts of
interest, funding, model access dates, computational settings, coder training,
and adjudication procedures in the final article.

## 9. Ethical and historiographical considerations

Salem records preserve accusations and coercive legal processes involving real
people. The project must not restate supernatural allegations as fact or reduce
historical persons to network nodes detached from the documentary setting.
Structured data can amplify archival asymmetries: the surviving record reflects
institutional procedures, selective preservation, and uneven power. Absence
from the sample is not evidence of historical absence, and co-occurrence is not
in itself a social relationship.

The pilot evaluates research infrastructure, not the truth of supernatural
claims. Its outputs require contextual interpretation by historians.

## 10. Limitations

The purposive sample cannot represent all Salem records, early modern legal
documents, or manuscript collections. A human reference is adjudicated rather
than infallible. Model behavior may change across snapshots, interfaces, and
image-processing pipelines. Exact-span requirements improve auditability but
may penalize defensible document-level interpretation. Finally, formal
agreement can conceal a shared misunderstanding; qualitative error review is
therefore essential.

## 11. Anticipated contribution

This project will contribute a versioned workflow and evaluation framework for
AI-assisted historical research. Its central proposition is simple: a useful
system must do more than produce a credible answer. It must expose the route
from source to claim, preserve uncertainty, and make interpretive overreach
detectable. The Salem pilot provides a demanding setting in which to test that
proposition while keeping the study small enough for close historical review.

## References

To be completed before submission. The final bibliography should cover Salem
archival editions and historiography; documentary editing and manuscript
transcription; provenance and data lineage; information extraction and entity
linking; inter-annotator agreement; uncertainty calibration; and evaluation of
language models in historical research.
