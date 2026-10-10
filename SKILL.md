---
name: skill-publisher
description: Package, version, and prepare a standalone or series skill for release to its configured GitHub subrepo.
metadata:
  version: 0.4.0
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
4. Run the completeness gate with `skillctl check --skill <skill-ref>`, or `PYTHONPATH=src python -m skill_manager check --skill <skill-ref>` when the package is not installed. Treat every reported issue as a release blocker and fix it before continuing.
5. Package with `skillctl package --skill <skill-ref>`. Packaging re-runs the completeness gate and refuses incomplete skills.
6. Before any GitHub publication, inspect the target repository and ensure it has a project description, topics, README, LICENSE, and a release matching the skill version.
7. Use `gh` as the preferred transport for repository creation, cloning, pushing, and release management.
8. If the configured repository does not exist, create it with `gh repo create <owner>/<repo> --private --description "<project description>"`. Use public visibility only when the user explicitly requests it.
9. Clone the repository, or reuse the configured publish clone, switch to the default branch (`main`), pull the latest changes, copy the skill folder into the configured target path, commit the exact files that belong to the skill, and push directly to `main`.
10. Publish a release named `v<version>` with concise release notes after the push.
11. Do not mutate target repositories automatically. State the exact target, then require explicit confirmation before any external mutation.

## Failure handling

- If a standalone skill has no configured `repo`, derive it from `github.owner` and the skill id.
- If a series has no configured `repo`, report that the package is ready but the publication target is unconfigured.
- If the same skill id exists in multiple series, use the explicit `<series>/<skill-id>` form.
- Fix validation errors before packaging or publishing.
- If the completeness gate fails, fix the reported issues first. Do not package, push, or open a release for an incomplete skill.
