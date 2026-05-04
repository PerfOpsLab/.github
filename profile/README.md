<div align="center">

# PerfOpsLab

### **Critical Systems Advisory**

**Launch-ready. Reliable. Spend under control.**

AI infra · performance · FinOps — one fight, not three silos.

Cyprus · revenue-critical teams only

</div>

---

Speed is not vanity. **Latency is margin.** Inference is not magic — it is **metered burn**. We fuse APM, logs, traces, load, and cloud bill into a single decision surface: what breaks revenue *first*, what costs *most*, who *owns* the fix.

**Sense.** One map: journeys, saturation, errors, unit economics.  
**Decide.** Ranked bottlenecks and spend leaks — impact, urgency, effort.  
**Govern.** Release gates, scaling rules, AI cost guardrails — named owners, no folklore.

**Signal Scan** — fast external read before you bet the quarter on a launch.  
**AI Infra Control Sprint** — baseline cost-per-request, full telemetry + billing pass, control memo for engineering, product, and finance.

No hundred-slide therapy. **Memos. Metrics. Motion.**

---

## PerfOps SaaS AI

PerfOps SaaS AI is the control plane between observability, CI/CD, cloud cost,
AI infrastructure cost, and release risk. The product turns operational noise
into evidence-backed decisions, recommendations, reports, and alerts.

### Core workflow

1. Connect customer infrastructure, CI/CD systems, observability tools, and cost sources.
2. Ingest signals from collectors, CI uploads, k6/JMeter runs, Prometheus, Grafana, cloud providers, and AI infrastructure systems.
3. Normalize evidence into tenant-scoped runs, snapshots, artifacts, and findings.
4. Score technical risk and economic impact with an explainable rule-based MVP, then add AI-assisted recommendations constrained by evidence.
5. Publish decisions, reports, notifications, tickets, and dashboards.

### System modules

| Module | Purpose | Primary repos |
| --- | --- | --- |
| Control Plane UI | Dashboard, onboarding, findings, reports, launch decisions, and account views. | `perfOps`, `perfops-consulting-site` |
| API / BFF | Tenant-aware product APIs for users, projects, integrations, findings, reports, billing, and entitlements. | `perfOps` |
| Ingestion | Customer collectors, CI uploads, k6/JMeter/Prometheus/cloud signals, and future AI infra cost feeds. | `perfOps`, `iac-monitoring-agent`, `perfops-action`, `perfops-sdk` |
| Analysis Engine | Evidence model, PerfOps score, findings, recommendations, reports, and AI-assisted explanations. | `perfOps`, `launchgate`, `reportkit` |
| Launch Gate | GO / CONDITIONAL_GO / NO_GO release decisions from SLO, load-test, cost, and reliability evidence. | `launchgate`, `perfops-action`, `perfops-sdk` |
| Storage | Postgres for SaaS entities, ClickHouse/time-series for high-volume metrics, object storage for artifacts. | `perfOps` |
| Integrations | GitHub, Slack, Linear, Jira, Grafana, Prometheus, cloud providers, and workflow automation. | `perfOps`, `perfops-action`, `grafana-dashboards` |
| Security | Tenant isolation, scoped tokens, audit trails, safe logging, secrets vault/OIDC roadmap. | `perfOps`, `launchgate` |

### Repository map

| Repository | Role |
| --- | --- |
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

### Near-term build order

1. Stabilize Launch Gate MVP: deterministic verdicts, k6/JMeter ingestion, SLO/NFR policy validation, GitHub Action output, and report artifacts.
2. Define SaaS tenant model and ingestion API: organizations, projects, scoped credentials, idempotency, retention classes, and customer collectors.
3. Build the analysis and recommendations lifecycle: evidence model, findings, score history, owner actions, reports, and AI-assisted explanations.
4. Add billing, entitlements, and enterprise security: Stripe, plan limits, audit trail, secrets vault/OIDC path, and scoped tokens.
5. Harden operations, dashboards, and customer collectors: preview/prod isolation, observability, backup/restore, load tests, and customer-facing deployment guides.

### Architecture principles

- Start as a modular monolith plus workers; split services only after clear scale or ownership pressure.
- Store control-plane entities and evidence references, not unlimited raw telemetry.
- Every recommendation must link back to evidence.
- Prefer deterministic policy and scoring for MVP decisions; use AI to explain, summarize, prioritize, and draft actions.
- Keep tenant boundaries explicit in data models, API contracts, secrets, artifacts, and audit events.
- Treat cost, performance, and reliability as one release-risk graph, not disconnected dashboards.
