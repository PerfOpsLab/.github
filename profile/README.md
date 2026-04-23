<div align="center">

# PerfOpsLab

**GitHub-организация** behind **[Critical Systems Advisory](https://maslinka.ohbah.com:8443/)** — *launch-readiness, reliability, and cloud efficiency* для команд, где инфраструктура и AI **влияют на выручку**.

[Лендинг](https://maslinka.ohbah.com:8443/) · [Book 20 min](https://maslinka.ohbah.com:8443/book) · [Pricing](https://maslinka.ohbah.com:8443/pricing) · [Код лендинга](https://github.com/PerfOpsLab/perfops-consulting-site)

</div>

---

## Сайт — источник правды по офферу

Всё, что **продукт, треки, доказательства и контакты** — на [maslinka.ohbah.com:8443](https://maslinka.ohbah.com:8443/). Здесь кратко то же позиционирование, плюс инженерный слой org.

**Фокус:** AI Infra / Performance / FinOps — скорость воронки, безопасные релизы, **стоимость запроса** и управляемый AI/spend.

**Петля решений (как на сайте):**

| Sense | Decide | Govern |
|--------|--------|--------|
| APM, логи, трейсы, нагрузка, биллинг в одной картине | Приоритет узких мест и утечек spend по **влиянию на выручку** и срочности | Guardrails релизов, масштабирования и **AI cost** с владельцами |

---

## Стартовые форматы (с лендинга)

- **Signal Scan** — быстрый внешний взгляд перед релизом, кампанией или инфра-решением; короткий shortlist узких мест и рисков.
- **AI Infra Control Sprint** — baseline cost-per-request / inference, разбор APM · logs · traces · billing · load, **control memo** для infra / product / finance.

Полный каталог и условия — на [Pricing](https://maslinka.ohbah.com:8443/pricing). Заявка и контакт — через форму на [главной](https://maslinka.ohbah.com:8443/).

---

## Инженерия под капотом (кратко)

Нагрузка (в т.ч. k6 + CI), наблюдаемость (Prometheus / Grafana / алерты), интеграции **Slack · Jira** и автоматизация (**n8n**, GitHub Actions). При необходимости — **auto-router моделей**, **редуктор логов перед LLM** под ваш стек.

---

## Инструментарий: AI-агенты без лишних токенов

Ниже — **универсальные промпты** для Cursor / Claude / GPT и multi-agent схем: один шаблон, жёсткий формат вывода, orchestrator + workers. Экономит контекст и ускоряет итерации на perf-задачах.

<details>
<summary><strong>Универсальный промпт (копипаста)</strong></summary>

```text
You are a senior {role}.

Mode:
- concise
- no explanations
- minimal tokens
- no repetition

Execution rules:
- process only given task
- do not use memory or previous context
- do not infer extra scope
- if unclear → assume and proceed

Output rules:
- structured only
- no free text
- no greetings

If task is:
- analysis → return key findings + actions
- debugging → return root cause + fix
- code → return minimal working code
- planning → return steps

Context:
{ONLY relevant trimmed data}

Task:
{clear specific instruction}

Return format:
{choose ONE: JSON | bullets | table | code}
```

</details>

<details>
<summary><strong>Добавки: STRICT · JSON · PERF/LOAD · DEBUG · CODE</strong></summary>

**STRICT**

```text
Return only final answer.
No explanations.
Maximize information density.
```

**JSON**

```text
Return ONLY valid JSON.
No text outside JSON.
```

**PERF / LOAD**

```text
Return:
- bottlenecks
- metrics to verify
- fixes (priority order)
```

**DEBUG**

```text
Return:
- root cause
- fix
```

**CODE**

```text
Return only code.
No explanations.
```

</details>

<details>
<summary><strong>Orchestrator + Worker</strong></summary>

**Orchestrator**

```text
You are an orchestration agent.

Goal:
Split task into minimal independent steps.

Rules:
- no execution
- no explanations
- minimal tokens

Output:
JSON only

Format:
{
  "steps": [
    {"task": "...", "agent": "..."}
  ]
}
```

**Worker**

```text
You are a {specialist}.

Rules:
- execute only given task
- no explanations
- minimal output

Task:
{task}
```

</details>

<details>
<summary><strong>Паттерн использования</strong></summary>

| Плохо | Хорошо |
|--------|--------|
| Один агент делает всё → много токенов | Orchestrator делит → workers исполняют |

**TL;DR:** один шаблон, единый формат ответа, меньше шума в контексте — удобно для multi-agent и perf-разборов.

</details>

---

<div align="center">

**PerfOpsLab** · Кипр · **[Critical Systems Advisory](https://maslinka.ohbah.com:8443/)**

[Book 20 min](https://maslinka.ohbah.com:8443/book) · [Открыть лендинг](https://maslinka.ohbah.com:8443/)

</div>
