---
name: skill-publisher
description: Package, version, and prepare a skill in this multi-series repository for release to its configured subrepo.
metadata:
  short-description: Publish skills from the multi-series repo
  version: 0.1.0
---

# Skill Publisher

Use this for release preparation of skills stored under `skills/<series>/<skill-id>/`. Do not use it to discover or install unrelated skills.

## Workflow

1. Resolve the target from `series.json` and `skills/<series>/<skill-id>/SKILL.md`. If the series is unregistered, stop and ask which series to use.
2. Confirm the release intent: whether the change is a patch, minor, or major bump, and whether the user wants packaging only or staging into the target subrepo.
3. Update the skill front matter version when a release bump is requested. Use semantic versioning: patch for fixes, minor for additive behavior, and major for breaking changes. If the intent is ambiguous, ask once.
4. Validate with `skillctl check`, or `PYTHONPATH=src python -m skill_manager check` when the package is not installed.
5. Package with `skillctl package --series <series>` or `skillctl package --skill <series>/<skill-id>`. The output is `dist/<series>/<skill-id>-<version>.zip`.
6. For GitHub publication, use the GitHub CLI (`gh`) as the preferred transport. Run `gh auth status` before any remote operation.
7. If the configured repository does not exist, create it with `gh repo create <owner>/<repo> --private --description "<series description>"`. Use public visibility only when the user explicitly requests it.
8. Clone with `gh repo clone <owner>/<repo> <staging-dir>`. If the repository is empty, initialize its default branch with a minimal README before adding skills.
9. Create a release branch named `publish/<skill-id>-<version>`, copy the skill folder into the configured target path, commit the exact files that belong to the skill, and push the branch.
10. Open a pull request with `gh pr create --fill`. Do not push directly to the default branch unless the user explicitly asks for a direct publish.
11. For release notes, summarize only behavior changes and installation impact. Use `writing-changelogs` when the user asks for a formal changelog or release note.
12. Do not push or mutate target repositories automatically. If the user asks to publish, read `repo` from `series.json`, state the exact target and branch, and require explicit confirmation before any external mutation.

## Failure handling

- If the series has no configured `repo`, report that the package is ready but the publication target is unconfigured.
- If the same skill id exists in multiple series, use the explicit `<series>/<skill-id>` form.
- Fix validation errors before packaging or publishing.
