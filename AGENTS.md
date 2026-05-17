---
title: muntianus — agent guidance (org canonical)
summary: Single source of truth for any AI coding agent working in any muntianus org repo.
status: stable
---

# muntianus — agent guidance

> **Read order:** this file → repo-local `AGENTS.md` (overrides), `CLAUDE.md` (Claude-specific notes), `GEMINI.md` (Gemini-specific notes).
> **Conflict rule:** repo-local file wins over org file. User instructions win over both.

## What we are

GitHub org **[muntianus](https://github.com/muntianus)** (PerfOpsLab org deleted). Ships **performance, reliability, FinOps, and AI-infra** tooling:

- **launchgate** — release gate engine (`GO / CONDITIONAL_GO / NO_GO`).
- **perfOps** — **production** SaaS dashboard (`apps/web` → `https://maslinka.ohbah.com:8443/dashboard`).
- **perfops-consulting-site** — archived Next.js funnel (not prod UI).
- **k6-scenarios, chaos-scenarios, benchmark-apps, nfr-library, perfops-templates** — content.
- **reportkit, perfops-action, perfops-sdk** — distribution.
- **iac-monitoring-agent, grafana-dashboards** — telemetry.

VPS host: `94.228.161.100`, ssh on port `5555`. Reverse proxy: Caddy on `:8443`.

## Hard rules — every repo

1. **No secrets in code, PRs, comments, or logs.** Reference variable names only (`HOSTINGER_TOKEN`, `OPENAI_API_KEY`, `LINEAR_API_KEY`, `VPS_SSH_KEY`). Local user secrets live in `~/.env`.
2. **Verify before declaring done.** Code change → run repo's verification (`make check`, `npm run check`, `go test ./...`, `pytest`, `k6 run … --vus 1 --duration 10s`). Reference the outcome in the PR body.
3. **Reuse over fork.** Use org-level reusable workflows in `.github/.github/workflows/`:
   - `reusable-security-gate.yml`
   - `reusable-ci-go.yml`
   - `reusable-ci-node.yml`
   - `reusable-deploy-vps.yml`
   Don't copy-paste workflow logic across repos.
4. **VPS boundary.** Touch only the directory of the service you own (e.g. `/home/jarvis/perfops-web`, `/root/launchgate`) and its systemd unit. Never edit `/etc/hysteria/**`, xray, hysteria, WireGuard, or unrelated Caddy blocks.
5. **Linear is the active backlog.** When work is tied to a Linear ticket, mention the ID in the PR body, move the ticket to `In Progress`, and only mark `Done` after verification.
6. **Token budget.** Auto-loaded files (this `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`) stay short. Detail goes into linked docs (load on demand).
7. **PR visibility.** Open a draft PR on first push so CI and other agents see the line of work. Bypass only if the human explicitly asked for a direct push to `main`.
8. **Docs update on substantive work.** If the change affects architecture, topology, infra, public contracts, or operational state, update the corresponding doc surface (see "Doc update protocol" below) in the same PR or a follow-up. Trivial work (typos, dependency bumps, single-file edits) is exempt — the auto session log captures it.

## Doc update protocol

The org keeps operational/architectural truth in three places. After
substantive work, update the relevant one. Do not silently change architecture
without updating diagrams.

| Surface | When to update | How |
|---|---|---|
| [`muntianus/architecture`](https://github.com/muntianus/architecture) | New service / removed component / topology shift / contract change (e.g. `verdict.json` schema) | Open PR; Mermaid renders on github.com — preview before push. Auto-deploys to <https://maslinka.ohbah.com:8443/docs/>. |
| Vault `STATUS.md` | Infra state change (Caddy edit, secret slot, service deploy, DNS), session-level decisions worth surfacing next time | Append a dated entry under "Что сделано" — never delete history. Auto-syncs to live `/graph` via the kb-sync hook. |
| Repo-local `docs/kb/<topic>.md` | Non-trivial finding (subtle bug, gotcha, root-cause writeup) tied to a specific repo | Use the kb frontmatter template; commit alongside the fix. |
| Vault `wiki/` (`muntianus/vault`) | Cross-repo knowledge: entity pages (per repo / infra), concept pages, ingested sources, autoresearch outputs | Edit per-page files or `wiki/<domain>/_index.md`; **never edit `wiki/index.md`** (regenerated). Stop hook commits + pushes with merge-driver retry. See "Vault Knowledge Base" below. |

**Helper skill** (Claude Code agents): invoke `update-perfops-docs` at session
end. It surveys git deltas and routes to the right surface. Other AI agents
follow this section by hand — same outcomes.

**Auto-mechanism for Claude Code:** the user's `~/bin/perfopslab-doc-update.sh`
runs as a Stop hook and appends a session block to
`~/Documents/Obsidian Vault/PerfOpsLab/sessions/YYYY-MM-DD.md`. This is the
*minimum* — it's a journal, not a substitute for the protocol above.

## Vault Knowledge Base

Org-wide knowledge base following the Karpathy LLM Wiki pattern, hosted at
[`muntianus/vault`](https://github.com/muntianus/vault) (private). Local mirror:
`~/Documents/Obsidian Vault/PerfOpsLab/`.

**Read order from any repo session:**

1. `wiki/hot.md` first (~500w cross-session cache, auto-regenerated by `~/bin/vault-update-hot`)
2. `wiki/index.md` (typed catalog: entities/concepts/sources/questions)
3. `wiki/<domain>/_index.md` for focused lookups
4. Individual wiki pages last

Skip the wiki for general coding questions or local-project context.

**Skills (claude-obsidian plugin v1.6.0+):** `/wiki`, `/wiki-ingest`, `/wiki-query`,
`/wiki-lint`, `/save`, `/autoresearch [topic]`. The `autoresearch` `program.md` is
customized for PerfOps domains (see `skills/autoresearch/references/program.md`).

### Vault Git Protocol (multi-writer)

The vault repo uses custom merge drivers in `.gitattributes` to support parallel
writes from N agents in M repos:

| File pattern | Driver | Resolution |
|---|---|---|
| `wiki/log.md` | `log-prepend` | concat + dedup by `(date, op, title)` + sort desc |
| `wiki/hot.md` | `keep-newer` | pick by `updated:` frontmatter; mtime fallback |
| `wiki/**/_index.md` | `union` | git's built-in union merge |
| `.raw/.manifest.json` | `manifest-merge` | sources union by path + address_map union |

**Setup (per-clone):** run `~/bin/vault-setup-merge-drivers .` once after `git clone`.

**Stop hook protocol:** `~/bin/perfopslab-doc-update.sh` runs at session end.
After writing the session entry to `sessions/YYYY-MM-DD.md`, it:

1. Regenerates `wiki/hot.md` via `~/bin/vault-update-hot`
2. `git pull --rebase origin main`
3. `git add -A && git commit && git push` with retry-on-conflict (3 attempts)

**Editor rules for agents:**

- Create new entity/concept/source: `wiki/<domain>/<Page Name>.md` (filename uniqueness — no conflict by construction)
- Update existing page: surgical `Edit` with unique `old_string`
- Update sub-index: `wiki/<domain>/_index.md` directly (union merge driver)
- Append to log: prepend new `## [date] op | title` entry to `wiki/log.md` (driver dedups)
- **Never** edit `wiki/index.md` directly — run `~/bin/vault-reindex` instead
- **Never** disable the Stop-hook auto-commit — merge drivers depend on it

## Per-language quick reference

| Stack | Verification | CI |
| --- | --- | --- |
| Go | `gofmt -l .`, `go vet ./...`, `go test -race -count=1 ./...`, `golangci-lint run` | `reusable-ci-go.yml` |
| Node / Next.js | `npm run check` (or lint+typecheck+test+build chain) | `reusable-ci-node.yml` |
| Python | `pytest`, `ruff check .` (when configured) | repo-local |
| k6 | `k6 run --vus 1 --duration 10s scenarios/<file>.js` smoke before any heavy run | `k6-ci.yml` per repo |

## VPS deploy contract

A repo that runs on the VPS exposes:

- A systemd unit named `<service>.service` in `~/<service>/` on the VPS (or `/root/<service>` for root-owned services).
- An internal health endpoint at `127.0.0.1:<port>/health`.
- `deploy/vps-deploy.sh` (or equivalent) that runs server-side after upload to install deps, build, and restart.

Wire the deploy with the org reusable workflow:

```yaml
jobs:
  deploy:
    uses: muntianus/.github/.github/workflows/reusable-deploy-vps.yml@main
    with:
      service_name: <service>.service
      app_dir: /home/jarvis/<service>
      source_glob: "<comma,separated,paths>"
      deploy_script: deploy/vps-deploy.sh
      health_url: http://127.0.0.1:<port>/health
      public_smoke_url: ${{ vars.PUBLIC_SMOKE_URL }}
    secrets:
      VPS_HOST: ${{ secrets.VPS_HOST }}
      VPS_USER: ${{ secrets.VPS_USER }}
      VPS_PORT: ${{ secrets.VPS_PORT }}
      VPS_SSH_KEY: ${{ secrets.VPS_SSH_KEY }}
```

## Issue / PR conventions

- **Issue titles:** `[EPIC] …`, `[TASK] …`, `[BUG] …`, `[CHORE] …`.
- **Labels:** use the canonical org label set (`priority/p0..p3`, `area/*`, `type/*`). Don't invent ad-hoc colors.
- **PR body:** what changed, why, link to Linear ticket if any, verification steps that were run.
- **Conventional commits** (`feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`).

## Repo manifest

See [`repos.yaml`](./repos.yaml) for the up-to-date list of repos, owners, and service info.

## Load on demand

| Need | Where |
| --- | --- |
| Repo-specific rules | repo's own `AGENTS.md` / `CLAUDE.md` |
| VPS infra map (services, ports, Caddy blocks) | `~/CLAUDE.md` (user) |
| Linear API access | `LINEAR_API_KEY` in `~/.env` |
| Hostinger DNS | `HOSTINGER_TOKEN` in `~/.env` |
