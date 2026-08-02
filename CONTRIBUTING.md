# Contributing

Contributions that improve the clarity, reproducibility, historical rigor, or practical implementation of the Historical Evidence Chain project are welcome.

## Before contributing

1. Read the relevant article section and the current codebook.
2. Check the [`roadmap/`](roadmap/) for known work and open questions.
3. Open a focused GitHub issue for substantial conceptual, schema, or protocol changes before implementing them.
4. Do not upload restricted archival images, credentials, private research notes, or identifiable participant data.

## Evidence-first requirements

Changes to research instruments should preserve these rules:

- source wording and normalized values remain separate;
- consequential observations include an exact source location;
- extraction is distinguished from normalization and inference;
- uncertainty and unresolved disagreement are recorded explicitly;
- document-specific roles are not converted automatically into enduring identities;
- warrants do not prove arrests, indictments do not prove convictions, shared surnames do not prove kinship, and co-occurrence does not prove a social relationship;
- supernatural allegations are represented as claims in historical records, not as independently established events;
- source text and previous transcription versions are never silently overwritten.

## Instrument versioning

Prompts, codebooks, controlled vocabularies, and data schemas are research instruments.

- Editorial corrections that do not change coding behavior may increment a patch version.
- Any change that can alter an annotation or model output should receive a new instrument version.
- Breaking field, identifier, or semantic changes require a major version.
- A locked test-set instrument must not be edited in place.
- Record the reason for a version change and identify affected downstream objects.

## Proposing changes

Keep each change focused. In the description, explain:

- what changed and why;
- which instrument versions are affected;
- whether the change alters prior coding or expected model behavior;
- what validation was performed;
- whether migration or recoding is required.

For codebook changes, include a positive example, a negative example, and at least one boundary case. For schema changes, document field type, null behavior, relationships, validation rules, and migration implications.

## Review checklist

- [ ] No restricted or sensitive files are included.
- [ ] Markdown links and headings render correctly.
- [ ] Identifiers and version strings are internally consistent.
- [ ] Controlled terms match the vocabulary specification.
- [ ] Every derived observation remains traceable to source evidence.
- [ ] No empirical finding is implied unless supported by deposited, reviewable results.

## Conduct

Discuss disagreement as a methodological resource. Historical sources can be ambiguous, and a defensible unresolved judgment is preferable to forced consensus. Contributions should be specific, respectful, and transparent about evidentiary limits.
