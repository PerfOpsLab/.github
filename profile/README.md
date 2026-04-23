<div align="center">

# PerfOpsLab

**Performance engineering для продуктовых команд:** нагрузка, узкие места, наблюдаемость, устойчивый релизный ритм.

[Сайт](https://maslinka.ohbah.com:8443/) · [Репозиторий лендинга](https://github.com/PerfOpsLab/perfops-consulting-site) · [Организация](https://github.com/PerfOpsLab)

</div>

---

## Зачем нам пишут

Мы помогаем, когда **рост трафика или сложность системы** упирается не в «ещё один инстанс», а в понятную картину: где теряется время, что измерять до и после изменений, как не ловить регрессии на проде.

| Вызов | Как работаем |
|--------|----------------|
| «Не знаем, выдержит ли релиз пик» | Сценарии нагрузки, SLO/бюджеты ошибок, проверяемые метрики |
| «Горит прод, логи бесконечные» | Сужение сигнала: трассировки, корреляции, приоритет фиксов |
| «Инженеры тонут в рутине» | Автоматизация пайплайнов, дашборды, дисциплина наблюдаемости |

Итог для бизнеса: **меньше неожиданных инцидентов, быстрее уверенные релизы, прозрачные цифры для продукта и финансов.**

---

## Что предлагаем (как B2B-партнёр по перфу)

1. **Диагностика и план** — карта рисков, quick wins vs фундамент.
2. **Нагрузочное и регрессионное покрытие** — k6 и связка с вашим CI/CD.
3. **Наблюдаемость под решения** — Grafana / Prometheus / алерты под ваш стек (в т.ч. Slack, Jira).
4. **Сопровождение релизов** — критерии готовности, постмортемы без воды.

Нужен **auto-router моделей**, **n8n / GitHub Actions**, **редуктор логов перед LLM** — собираем под ваш стек (k6, Grafana, Slack, Jira и др.).

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

**PerfOpsLab** · Кипр · Инженерия производительности и надёжности продукта

*Напишите, с чего начать: нагрузка, инцидент, аудит наблюдаемости или зрелость пайплайна.*

</div>
