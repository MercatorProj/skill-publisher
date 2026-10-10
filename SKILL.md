---
name: skill-publisher
description: Package, version, and prepare a standalone or series skill for release to its configured GitHub subrepo.
metadata:
  version: 0.2.0
  license: MIT
  topics: [codex, skills, publishing, github-cli, release]
---

# Skill Publisher

Prepare and publish a personal Codex skill. Two layouts are supported:

- Standalone skill: `skills/<skill-id>/` publishes to the same-named repository.
- Series skill: `skills/<series>/<skill-id>/` publishes to the repository configured for that series.

Do not use this skill to discover or install unrelated skills.

## Workflow

1. Resolve the layout from `series.json` and the skill's `SKILL.md`.
2. Confirm the release intent and whether the change is a patch, minor, or major bump.
3. Update the skill front matter version when a release bump is requested. Use semantic versioning.
4. Validate with `skillctl check`, or `PYTHONPATH=src python -m skill_manager check` when the package is not installed.
5. Package with `skillctl package --skill <skill-ref>`.
6. Before any GitHub publication, inspect the target repository and ensure it has a project description, topics, README, LICENSE, and a release matching the skill version.
7. Use `gh` as the preferred transport for repository creation, cloning, pushing, and release management.
8. If the configured repository does not exist, create it with `gh repo create <owner>/<repo> --private --description "<project description>"`. Use public visibility only when the user explicitly requests it.
9. Clone the repository, create a release branch, copy the skill folder into the configured target path, commit the exact files that belong to the skill, and push the branch.
10. Open a pull request with `gh pr create --fill`. Do not push directly to the default branch unless the user explicitly asks for a direct publish.
11. After the release is merged, publish a release named `v<version>` with concise release notes.
12. Do not mutate target repositories automatically. State the exact target and branch, then require explicit confirmation before any external mutation.

## Failure handling

- If a standalone skill has no configured `repo`, derive it from `github.owner` and the skill id.
- If a series has no configured `repo`, report that the package is ready but the publication target is unconfigured.
- If the same skill id exists in multiple series, use the explicit `<series>/<skill-id>` form.
- Fix validation errors before packaging or publishing.
