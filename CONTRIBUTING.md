# Contributing to muntianus org repos

The canonical workflow rules live in [`AGENTS.md`](./AGENTS.md) — read it
first. This file covers contributor-facing branch model only.

## Branch model
- `main` is the integration branch and is the only branch that auto-deploys.
- Feature work happens on `feat/*`, `fix/*`, `chore/*` branches.

## Pre-merge checklist
- All checks green (CI, security gate).
- PR body links to the Linear ticket (if any) and lists verification commands actually run.
- New `.md` files have frontmatter when the repo's knowledge graph requires it.
- All [hard rules](./AGENTS.md#hard-rules--every-repo) are satisfied.

For draft-PR policy, reusable workflow names, issue/PR title prefixes, label
scheme, and conventional commit prefixes, see [`AGENTS.md`](./AGENTS.md).
