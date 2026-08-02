# Data and Archival Image Policy

**Policy ID:** `SAL-DATA-POLICY-1.0`

This repository does **not** redistribute restricted archival images. Repository access, digitization, and reuse terms may differ from copyright status; the project honors the applicable holding repository's conditions and documented permissions.

## What may be committed here

- synthetic fixtures that are unmistakably labeled as synthetic;
- public-domain or permission-cleared derivatives with documented provenance and approval;
- source identifiers, archival citations, rights/access classifications, and non-secret checksums;
- transcriptions, annotations, and aggregate outputs only to the extent allowed by applicable terms;
- manifests and validation reports that contain no restricted URL, token, credential, or image payload.

## What must not be committed

- restricted or permission-unclear scans, photographs, thumbnails, crops, or screenshots;
- files obtained through repository systems whose terms prohibit redistribution;
- signed URLs, session cookies, access tokens, credentials, or local paths exposing controlled storage;
- a Git LFS pointer to material that would be prohibited as an ordinary Git object. Git LFS is not a rights workaround.

## Referencing an unavailable image

Use a stable project `source_id`, full archival citation, repository-supplied identifier, `rights_status`, and `rights_access_note`. Add a SHA-256 checksum only when the team is authorized to hold the referenced local file. Never publish a time-limited or credential-bearing URL.

## Authorized working copies

If the project is permitted to retain working images, keep them in institutionally approved restricted storage outside the Git repository. Limit access to authorized team members, maintain the repository's required attribution, and follow the applicable retention/deletion schedule. The data export should contain a controlled reference, not the binary image.

## Release review

Before a public release, a designated steward must:

1. verify the rights/access status of every page;
2. inspect the Git history as well as the working tree for prohibited assets;
3. confirm that metadata and derived text are permitted for release;
4. remove secrets and non-public asset locations from manifests and logs;
5. record exclusions and unresolved rights questions in the release manifest.

If an image was committed accidentally, stop distribution and follow the project's incident procedure. Deleting the current file is insufficient because Git history may retain the object.
