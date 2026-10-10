# skill-publisher

**Version:** 0.5.0  
**License:** MIT

Validate, version, package, and safely publish owned Codex skills from a skill-manager repository to configured GitHub repositories. It supports standalone skills and skills grouped into series.

## What's new in 0.5.0

- Separates audit/check, local packaging, and remote publishing modes.
- Fixes the preflight/postflight ordering: an existing Release is checked for version collisions before publication and verified after publication.
- Uses the target repository's actual default branch rather than assuming `main`.
- Adds explicit release-plan confirmation and protections for repository visibility, branch changes, existing tags/releases, dirty worktrees, unrelated files, and concurrent remote changes.
- Requires archive and manifest inspection, plus independent post-publication verification.
- Improves partial-failure recovery and prevents blind repeat operations.

## Supported layouts

| Layout | Source path | Target |
|---|---|---|
| Standalone | `skills/<skill-id>/` | `<github.owner>/<skill-id>` |
| Series | `skills/<series>/<skill-id>/` | Repository and path configured for that series |

Example:

```text
skills/skill-publisher          -> MercatorProj/skill-publisher
skills/audit-anything           -> MercatorProj/audit-anything
skills/research/paper-analyzer  -> repository configured for research
```

## Requirements

- Python 3.10 or newer, as required by the manager tooling
- `git`
- GitHub CLI (`gh`) authenticated for the required target operations
- A valid `series.json` with `github.owner` and any required series destinations
- The skill manager's `skillctl` command, or its documented Python module fallback

Use only permissions needed for the specific target. Organization-specific scopes may be necessary for organization repositories; do not broaden access unnecessarily.

## Quick start

From the skill-manager repository:

```bash
skillctl check --skill skill-publisher
skillctl package --skill skill-publisher
```

If the entry point is not installed and the manager repository supports this fallback:

```bash
PYTHONPATH=src python -m skill_manager check --skill skill-publisher
PYTHONPATH=src python -m skill_manager package --skill skill-publisher
```

For a skill in a series:

```bash
skillctl check --skill research/paper-analyzer
skillctl package --skill research/paper-analyzer
```

Exact commands and artifact paths are determined by the installed manager version. Inspect the command output; do not assume an artifact filename.

## Recommended workflow

1. Resolve the skill source and publishing target from actual configuration.
2. Inspect the current version and the change scope; do not bump the version merely to rebuild an archive.
3. Run `skillctl check` and fix every blocker.
4. Run `skillctl package`; inspect the ZIP and `MANIFEST.json`, including for unintended files or secrets.
5. Inspect the target repository and its actual default branch. Check for existing version tags and Releases.
6. Present the exact release plan and obtain explicit confirmation before remote mutations.
7. Stage only the skill's validated files and inspect the diff before committing.
8. Create the versioned GitHub Release only after the target content is published; attach the validated package ZIP when included in the plan.
9. Verify the remote commit, tag, Release, asset, and repository metadata.

## Safety model

The skill can prepare a local package without publishing it. Remote changes require a concrete plan and explicit confirmation. It must not force-push, rewrite existing release tags, discard user changes, change visibility without authorization, or copy the manager repository wholesale into a skill repository.

The skill's instructions do not themselves execute a publication. Results depend on the available tools, repository configuration, credentials, branch protections, and the actual command results. Checks not run must be reported as not verified.

## Release metadata

**Before publishing:** repository description, topics, README, LICENSE, correct skill content, and any required configuration must be ready.

**After publishing:** verify the version tag, GitHub Release, release notes, and planned package asset. A matching Release is not a prerequisite for creating a first release of that version.

## Semantic versioning

- `PATCH`: compatible fixes and clarifications.
- `MINOR`: additive behavior that preserves the established interface and intent.
- `MAJOR`: breaking changes to workflow, interface, supported layout, or metadata contract.

Existing tags and Releases are immutable by default. If the target version already exists with different content, publish a new version rather than overwriting history.

## Scope and limits

This skill targets personal or otherwise authorized skill repositories. It does not discover or install unrelated skills, bypass repository protection, or guarantee publication where the available credentials or tools lack permission.

## License

MIT. See `LICENSE`.
