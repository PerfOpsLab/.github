# Contributing to PerfOpsLab

## Branch model
- `main` is the integration branch and is the only branch that auto-deploys.
- Feature work happens on `feat/*`, `fix/*`, `chore/*` branches.
- Open a draft PR on the first push so CI is visible to reviewers and agents.

## Pre-merge checklist
- All checks green (CI, security gate).
- PR body links to the Linear ticket (if any) and lists verification commands.
- New `.md` files have frontmatter when the repo's knowledge graph requires it.
- No secrets in source, comments, logs, or PR descriptions — reference variable names only.

## Reusable workflows
Use the org-level reusable workflows from [`PerfOpsLab/.github`](https://github.com/PerfOpsLab/.github/tree/main/.github/workflows):

- `reusable-security-gate.yml`
- `reusable-ci-go.yml`
- `reusable-ci-node.yml`
- `reusable-deploy-vps.yml`

Don't fork the same logic into per-repo workflows.

## Issue conventions
- Title prefix: `[EPIC]`, `[TASK]`, `[BUG]`, `[CHORE]`.
- Labels: priority (`priority/p0..p3`), area (`area/*`), type (`type/*`). Use the canonical org label set.
- Use the issue templates in `.github/ISSUE_TEMPLATE/`.
