# PerfOps SaaS AI Architecture

PerfOps SaaS AI is a control plane between observability, CI/CD, cloud cost,
AI infrastructure cost, and release risk. The product goal is to turn noisy
operational signals into evidence-backed decisions, recommendations, reports,
and alerts that engineering and business owners can act on.

This document is the organization-level architecture map for the PerfOpsLab
repository portfolio. It links the product direction tracked in Linear
(`YAP-118`, `YAP-141`) with the GitHub repositories that own each subsystem.

## Core Workflow

1. Connect customer infrastructure, CI/CD systems, observability tools, and cost
   sources.
2. Ingest signals from collectors, CI uploads, k6/JMeter runs, Prometheus,
   Grafana, cloud providers, and AI infrastructure systems.
3. Normalize evidence into tenant-scoped runs, snapshots, artifacts, and
   findings.
4. Score technical risk and economic impact with an explainable rule-based MVP,
   then add AI-assisted recommendations constrained by evidence.
5. Publish decisions, reports, notifications, tickets, and dashboards.

## System Modules

### Control Plane UI

The user-facing product surface for onboarding, connected systems, findings,
reports, launch decisions, and account-level views. It should stay focused on
scannable operational workflows rather than marketing pages.

Primary repository:

- `PerfOpsLab/perfOps`

Related surface:

- `PerfOpsLab/perfops-consulting-site`

### API / BFF

Tenant-aware product APIs for organizations, users, memberships, projects,
integrations, signal submissions, findings, reports, billing state, and
entitlements. The near-term shape is a modular monolith with workers, not
premature microservices.

Primary repository:

- `PerfOpsLab/perfOps`

Roadmap references:

- `YAP-138` / GitHub `#227`: backend architecture
- `YAP-139` / GitHub `#226`: tenant model and Postgres migration

### Ingestion

Customer-side and CI-side ingestion for load-test results, SLO policies,
traffic models, metrics snapshots, cost signals, and future collectors. Ingest
must be tenant-scoped, idempotent, rate-limited, and safe for customer
infrastructure data.

Primary repositories:

- `PerfOpsLab/perfOps`
- `PerfOpsLab/iac-monitoring-agent`
- `PerfOpsLab/perfops-action`
- `PerfOpsLab/perfops-sdk`

Supporting repositories:

- `PerfOpsLab/k6-scenarios`
- `PerfOpsLab/benchmark-apps`

Roadmap reference:

- `YAP-137` / GitHub `#228`: ingestion API and customer collector

### Analysis Engine

The analysis engine turns normalized evidence into scores, findings,
recommendations, launch verdicts, and executive summaries. The MVP should be
rule-based and deterministic. AI recommendations should only explain or
prioritize evidence that the system can trace back to collected signals.

Primary repositories:

- `PerfOpsLab/perfOps`
- `PerfOpsLab/launchgate`
- `PerfOpsLab/reportkit`

Supporting repositories:

- `PerfOpsLab/nfr-library`
- `PerfOpsLab/perfops-templates`
- `PerfOpsLab/chaos-scenarios`

Roadmap reference:

- `YAP-135` / GitHub `#230`: analysis engine and recommendations lifecycle

### Launch Gate

Launch Gate is the release-decision engine. It evaluates load-test evidence,
SLO/NFR policies, reliability signals, and cost guardrails, then produces
`GO`, `CONDITIONAL_GO`, or `NO_GO` outcomes for CI and release workflows.

Primary repositories:

- `PerfOpsLab/launchgate`
- `PerfOpsLab/perfops-action`
- `PerfOpsLab/perfops-sdk`

Supporting repositories:

- `PerfOpsLab/k6-scenarios`
- `PerfOpsLab/nfr-library`
- `PerfOpsLab/perfops-templates`
- `PerfOpsLab/grafana-dashboards`

Roadmap references:

- `YAP-236`: Launch Gate backend sprints
- `YAP-257`: Launch Gate MVP backend core

### Storage

Storage is split by data shape:

- Postgres for transactional SaaS entities such as tenants, users, memberships,
  projects, integrations, runs, findings, reports, billing state, and
  entitlements.
- ClickHouse or a time-series store for high-volume events, metric aggregates,
  and long-range analysis.
- S3-compatible object storage for uploaded artifacts, generated reports,
  snapshots, and replayable evidence bundles.

Primary repository:

- `PerfOpsLab/perfOps`

Roadmap references:

- `YAP-136` / GitHub `#229`: highload storage strategy
- `YAP-139` / GitHub `#226`: core tenant model and Postgres migration

### Integrations

Integrations publish decisions and synchronize work into the systems teams
already use: GitHub, Slack, Linear, Jira, Grafana, Prometheus, and cloud
providers. Integrations must preserve evidence links and avoid storing raw
secrets outside the chosen secret boundary.

Primary repositories:

- `PerfOpsLab/perfOps`
- `PerfOpsLab/perfops-action`
- `PerfOpsLab/grafana-dashboards`

Roadmap reference:

- `YAP-134` / GitHub `#231`: notifications and workflow integrations

### Security

Security is a product feature because PerfOps handles infrastructure metadata,
performance signals, cost data, and potentially sensitive business context.
The platform must support tenant isolation, scoped API tokens, audit trails,
safe logging, secrets hygiene, and a path to vault-backed credentials and OIDC.

Primary repository:

- `PerfOpsLab/perfOps`

Related repository:

- `PerfOpsLab/launchgate`

Roadmap reference:

- `YAP-132` / GitHub `#233`: security, compliance, and enterprise readiness

### Billing And Entitlements

Billing should map product value to usage and packaging: seats, projects,
ingested volume, retention, integrations, launch gates, and enterprise contract
controls. Entitlements must be enforced in the API and reflected in UI states.

Primary repository:

- `PerfOpsLab/perfOps`

Roadmap reference:

- `YAP-133` / GitHub `#232`: billing, entitlements, and usage metering

### Operations

The operating model starts with a modular monolith plus workers and grows only
where scaling pressure requires separation. CI/CD, preview/prod isolation,
load testing, backups, observability, and rollback paths are part of the
product architecture, not afterthoughts.

Primary repositories:

- `PerfOpsLab/perfOps`
- `PerfOpsLab/launchgate`
- `PerfOpsLab/grafana-dashboards`
- `PerfOpsLab/order-lab`

Roadmap reference:

- `YAP-131` / GitHub `#234`: operations, deployment path, and load testing

## Repository Map

| Repository | Role |
| --- | --- |
| `.github` | Organization profile, repository map, shared standards, and architecture hub. |
| `perfOps` | Main SaaS AI control plane for product APIs, UI, workers, tenancy, storage, analytics, and billing. |
| `perfops-consulting-site` | Consulting funnel and SaaS product shell during the transition from service business to product. |
| `launchgate` | CLI/API release gate engine for launch decisions. |
| `perfops-action` | GitHub Action that brings launch gates into CI. |
| `perfops-sdk` | SDKs for integrating PerfOps workflows into customer tools. |
| `perfops-templates` | SLO, NFR, traffic model, and launch-readiness templates. |
| `k6-scenarios` | Reusable k6 scenarios for validation and demos. |
| `benchmark-apps` | Reference applications for load-testing demos and tooling validation. |
| `nfr-library` | Vertical-specific NFR and SLO catalog. |
| `chaos-scenarios` | Resilience and launch-readiness failure scenarios. |
| `grafana-dashboards` | Dashboard-as-code for observability and launch gate results. |
| `reportkit` | Markdown and PDF reporting for verdicts and executive summaries. |
| `iac-monitoring-agent` | Prototype customer-side infrastructure signal collector. |
| `order-lab` | Reference Kafka/Prometheus service for tests and demos. |

## Near-Term Build Order

1. Stabilize Launch Gate MVP: deterministic verdicts, k6/JMeter ingestion,
   SLO/NFR policy validation, GitHub Action output, and report artifacts.
2. Define SaaS tenant model and ingestion API: organizations, projects,
   scoped credentials, idempotency, retention classes, and customer collectors.
3. Build the analysis and recommendations lifecycle: evidence model, findings,
   score history, owner actions, reports, and AI-assisted explanations.
4. Add billing, entitlements, and enterprise security: Stripe, plan limits,
   audit trail, secrets vault/OIDC path, and scoped tokens.
5. Harden operations, dashboards, and customer collectors: preview/prod
   isolation, observability, backup/restore, load tests, and customer-facing
   deployment guides.

## Architecture Principles

- Start as a modular monolith plus workers; split services only after clear
  scale or ownership pressure.
- Store control-plane entities and evidence references, not unlimited raw
  telemetry.
- Every recommendation must link back to evidence.
- Prefer deterministic policy and scoring for MVP decisions; use AI to explain,
  summarize, prioritize, and draft actions.
- Keep tenant boundaries explicit in data models, API contracts, secrets,
  artifacts, and audit events.
- Treat cost, performance, and reliability as one release-risk graph, not
  disconnected dashboards.
