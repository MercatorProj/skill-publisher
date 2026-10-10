# skill-publisher

Package, version, and publish Codex skills to GitHub. It is built for a small personal skill monorepo where some skills are standalone and others are grouped into series.

## Why it exists

Managing skills in one place is convenient, but publishing them one by one to GitHub is repetitive and easy to get wrong. This skill standardizes that process so each release has the same structure and the same release hygiene:

- a validated skill folder;
- a completeness gate that blocks packaging until required files and metadata are present;
- a semantic version bump;
- a clean package archive;
- a named target repository;
- complete GitHub metadata;
- a versioned GitHub release.

It is meant for publishing your own skills. It does not discover or install unrelated skills.

## Repository mapping

`skill-publisher` supports two layouts:

| Layout | Source path | Target |
|---|---|---|
| Standalone | `skills/<skill-id>/` | `<github.owner>/<skill-id>` |
| Series | `skills/<series>/<skill-id>/` | repository configured for `<series>` |

Examples:

```text
skills/skill-publisher          -> MercatorProj/skill-publisher
skills/audit-anything           -> MercatorProj/audit-anything
skills/research/paper-analyzer  -> repository configured for research
```

The standalone mapping is intentional: a standalone skill should live in a repository with the same name, so the repository is easy to find and the skill can be published without configuring a separate target.

## Requirements

- Python 3.10 or newer
- `git`
- GitHub CLI (`gh`) with `repo`, `workflow`, and `read:org` scopes as needed
- a configured `series.json` with `github.owner`

## Quick start

From the skill manager repository:

```bash
skillctl check --skill skill-publisher
skillctl package --skill skill-publisher
```

When the `skillctl` entry point is not installed, run the same check from the skill manager repository with:

```bash
PYTHONPATH=src python -m skill_manager check --skill skill-publisher
```

For a skill in a series:

```bash
skillctl package --skill research/paper-analyzer
```

The package output is a deterministic zip archive with a `MANIFEST.json` describing the skill id, version, license, topics, and entrypoint.

## Publishing workflow

1. Confirm whether the change is a patch, minor, or major release.
2. Bump the version in the skill's `SKILL.md`.
3. Run the completeness gate: `skillctl check --skill <skill-ref>`. Every reported issue is a release blocker.
4. Package the skill. Packaging re-runs the gate and refuses incomplete skills.
5. Check the target repository metadata.
6. Copy only the skill's files into the target repository.
7. Commit and push directly to the default branch (`main`).
8. Create a GitHub release matching the version.

## Release metadata checklist

Before publishing to GitHub, this skill treats the following as required:

| Item | Purpose |
|---|---|
| Description | Makes the repository understandable in search and preview contexts |
| Topics | Helps discovery and grouping |
| README | Explains purpose, installation, usage, and limits |
| LICENSE | States reuse terms |
| Release | Marks the published version and changes |

The completeness gate enforces the skill-folder side of this checklist automatically. A skill passes only when:

- `SKILL.md` has `name`, `description`, and a semantic `metadata.version`;
- front matter declares `metadata.license` and a non-empty `metadata.topics` list;
- `README.md` exists, has at least one heading, and is substantial enough to explain the skill;
- a `LICENSE` file exists in the skill folder.

`skillctl package` refuses to build an archive when any of these checks fail, so an incomplete skill cannot reach the packaging step silently.

The skill uses `gh` for repository creation, metadata updates, cloning, pushing, and releases.

## Safety model

This skill does not silently mutate remote repositories. It states the target repository first, then waits for explicit confirmation before:

- creating a repository;
- pushing to the default branch (`main`);
- changing visibility;
- creating or editing a release.

This keeps accidental publishes, visibility changes, and destructive updates unlikely.

## Common cases

### New standalone skill

Place it at:

```text
skills/my-skill/SKILL.md
```

It will target:

```text
<github.owner>/my-skill
```

### New series

Add it to `series.json`:

```json
{
  "series": {
    "research": {
      "description": "Research and analysis skills",
      "repo": "https://github.com/MercatorProj/research-skills.git",
      "path": "skills"
    }
  }
}
```

Then place skills under:

```text
skills/research/<skill-id>/
```

All skills in that series share the same repository.

### Version bump

Use semantic versioning:

- patch: documentation or small fixes;
- minor: additive behavior;
- major: breaking workflow or interface change.

## Troubleshooting

- Missing `repo` for a series: configure it in `series.json`.
- Missing `github.owner`: add it to `series.json`.
- Duplicate skill id: use `<series>/<skill-id>` explicitly.
- Failed validation: fix `SKILL.md` before packaging or publishing.
- Completeness gate failures: read the reported issues from `skillctl check --skill <skill-ref>`, fix them in the skill folder, and run the check again before packaging.

## License

MIT.
