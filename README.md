# Historical Evidence Chain

Research materials for the **Salem Historical Evidence Chain** pilot: a framework for making AI-assisted historical research valid, transparent, reproducible, and inspectable from archival source to Historical Claim.

The pilot uses records associated with the Salem witch trials to study three linked operations:

1. diplomatic manuscript transcription;
2. source-grounded entity and relation extraction; and
3. audit of evidence chains and downstream Historical Claims.

The project treats an AI system as a probabilistic measurement instrument. Model output is therefore provisional until it has been bound to source evidence, evaluated for its intended use, and—where the stakes require it—reviewed or adjudicated by a human researcher.

## Evidence-chain model

```text
archival source → transcription → source-bound observation → validation → analysis → Historical Claim → downstream audit
```

Each transformation should retain its input, procedure, version, uncertainty, validation status, and downstream dependencies. A plausible output without inspectable source support is not accepted as evidence.

## Repository guide

| Path | Contents |
|---|---|
| [`docs/article/`](docs/article/) | Draft article sections introducing the historical evidence-chain framework and empirical design |
| [`docs/codebook/`](docs/codebook/) | Salem codebook and the parallel human coding form |
| [`prompts/`](prompts/) | Versioned working experimental prompts for transcription, extraction, and audit |
| [`schemas/`](schemas/) | Practical Airtable and REDCap implementation specifications |
| [`metadata/`](metadata/) | Controlled vocabularies, identifiers, provenance fields, and schema notes |
| [`data/README.md`](data/README.md) | Data-access and non-redistribution policy |
| [`roadmap/`](roadmap/) | Planned work and issue backlog |

## Current instrument versions

| Instrument | Version |
|---|---|
| Salem codebook | `SAL-CB-0.3` |
| Human coding form | `SAL-HUMAN-CODING-FORM-1.0` |
| Transcription prompt | `SAL-PROMPT-TRANSCRIPTION-1.0` |
| Extraction prompt | `SAL-PROMPT-EXTRACTION-1.0` |
| Audit prompt | `SAL-PROMPT-AUDIT-1.0` |
| Airtable schema | `SAL-AIRTABLE-1.0` |
| REDCap schema | `SAL-REDCAP-1.0` |

Version identifiers describe research instruments, not completed or validated study results. Prompts and schemas should be frozen before use on a locked test set; later changes should receive new versions and be documented.

## Starting points

- For the argument and research design, begin with [`docs/article/`](docs/article/).
- For human annotation, read the codebook before using the coding form.
- For a relational implementation with many-to-many links, use the Airtable specification.
- For formal coder data collection and institutional audit trails, use the REDCap specification.
- Before adding any source material, read [`data/README.md`](data/README.md).

## Data and archival rights

Restricted archival images are **not redistributed** in this repository. Researchers must obtain source materials from the holding archive or another authorized provider and comply with the relevant access, copyright, privacy, and reuse conditions. Local filenames, checksums, and archive identifiers may be recorded to support reproducibility without republishing the images themselves.

## Project status

This is an initial research scaffold. It contains draft prose, versioned research instruments, and implementation specifications. It does not contain archival images, participant data, model outputs, adjudicated reference data, or empirical findings.

## Contributing and citation

See [`CONTRIBUTING.md`](CONTRIBUTING.md) before proposing a change. Citation metadata are provided in [`CITATION.cff`](CITATION.cff).

## License status

No project license has yet been selected. [`LICENSE`](LICENSE) is intentionally a placeholder; no permission should be inferred from the repository's visibility. Third-party archival materials remain subject to their own rights statements.
