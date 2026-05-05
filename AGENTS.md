---
title: PerfOpsLab — agent guidance (org canonical)
summary: Single source of truth for any AI coding agent working in any PerfOpsLab repo.
status: stable
---

# PerfOpsLab — agent guidance

> **Read order:** this file → repo-local `AGENTS.md` (overrides), `CLAUDE.md` (Claude-specific notes), `GEMINI.md` (Gemini-specific notes).
> **Conflict rule:** repo-local file wins over org file. User instructions win over both.

## What we are

PerfOpsLab ships **performance, reliability, FinOps, and AI-infra** tooling. The product line:

- **launchgate** — release gate engine (`GO / CONDITIONAL_GO / NO_GO`).
- **perfOps** — SaaS control plane (UI + BFF on top of launchgate).
- **perfops-consulting-site** — consulting funnel (Next.js 16, lives on a single VPS).
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
4. **VPS boundary.** Touch only the directory of the service you own (e.g. `/home/jarvis/perfops-consulting-site`, `/root/launchgate`) and its systemd unit. Never edit `/etc/hysteria/**`, xray, hysteria, WireGuard, or unrelated Caddy blocks.
5. **Linear is the active backlog.** When work is tied to a Linear ticket, mention the ID in the PR body, move the ticket to `In Progress`, and only mark `Done` after verification.
6. **Token budget.** Auto-loaded files (this `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`) stay short. Detail goes into linked docs (load on demand).
7. **PR visibility.** Open a draft PR on first push so CI and other agents see the line of work. Bypass only if the human explicitly asked for a direct push to `main`.

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
    uses: PerfOpsLab/.github/.github/workflows/reusable-deploy-vps.yml@main
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
