# Contributing

Contributions should preserve the specification's stability, portability, and open-source maintainability.

## Development Rules

- Prefer small incremental commits over monolithic rewrites.
- Keep schema changes backwards compatible unless a major version is proposed.
- Add or update examples when changing field semantics.
- Document modality extensions independently from the core schema.
- Use semantic versioning for public releases.
- Add changelog entries for user-visible changes.

## Proposal Process

1. Open an issue describing the proposed change.
2. Identify whether the change affects the core schema, an extension, examples, or documentation.
3. Include migration notes for schema changes.
4. Submit focused pull requests.

## Medical and Clinical Content

Medical examples must be synthetic, non-diagnostic, and clearly labeled for educational or research use. Do not include real patient data, fake clinical reports, fake DICOM exports, or fabricated hospital studies.
