# Changelog

## 0.5.0 — 2026-10-10

### Added
- Explicit operating modes for audit/check, local packaging, and remote publishing.
- Concrete release-plan confirmation requirements.
- Archive-content and manifest inspection requirements.
- Post-publication read-back verification and partial-failure recovery guidance.
- Protections against accidental secrets, unrelated files, dirty worktrees, concurrent remote changes, and duplicate release versions.

### Changed
- Determine the target repository's default branch dynamically instead of assuming `main`.
- Separate preflight repository metadata checks from postflight release verification.
- Clarify semantic-version selection and avoid bumping versions merely to rebuild packages.

### Compatibility
- Retains the original standalone and series repository-mapping model.
- Retains the `skillctl check` and `skillctl package` workflow, including the documented Python module fallback.
