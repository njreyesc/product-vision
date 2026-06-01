# Карта интеграции: наши скиллы ↔ существующие плагин-скиллы и MCP

**Дата:** 2026-05-31
**Контекст продукта (пример):** платформа оценки рисков (risk assessment).
**Важно:** карта связи. Сами скиллы и интеграции по ней пока **не создаются**.

Наши 4 скилла (`product-vision`, `business-architecture`, `it-architecture`, `architecture-validator`) — это **слой-каркас**: цепочка + базовый handoff-контракт (`id`/`type`/`text`/`trace_up`/`source`/`status`/`owner`/`version`) + `covers` для ИТ/mapping + gate + трассируемость. Существующие плагин-скиллы и MCP — это целевые движки и источники внутри этого каркаса; доступность каждой интеграции проверяется при реализации.

---

## Карта связи (цепочка ↔ плагины)

```
                          СТРАТЕГИЯ / РЫНОК / РЕГУЛЯТОР
                                      │
   ┌──────────────────────────────────────────────────────────────────────┐
   │  НАШ СЛОЙ-ДИРИЖЁР (спроектированные ТЗ-скиллы)                         │
   └──────────────────────────────────────────────────────────────────────┘
                                      │
   ① product-vision (зачем)
      использует:
      • product-management: brainstorm / product-brainstorming
      • product-management: competitive-brief / synthesize-research
      • design: user-research / research-synthesis
      • marketing: competitive-brief
      • bigdata-com: risk-assessment, scenario-analysis  ← доменные (риск-продукт)
                                      │
                                      ▼
   ② business-architecture (что)
      использует:
      • product-management: write-spec / roadmap-update / sprint-planning
      • product-tracking: model-product / design-tracking-plan / business-case
      • design: design-system
                                      │
                                      ▼
   ③ it-architecture (как)
      использует:
      • engineering: architecture (ADR) / system-design
      • engineering: tech-debt / testing-strategy / documentation
      • mcp-builder, data: sql-queries / build-dashboard
                                      │
                                      ▼
   ④ architecture-validator (gate)
      использует:
      • engineering: code-review / built-in code-review
      • verify, security-review, data: validate-data
      • product-tracking: audit-current-tracking
                                      │
                                      ▼
                        ⟲ обратная связь наверх
```

---

## Три роли плагин-скиллов относительно наших

```
            ┌─────────────────────────────────────────────┐
            │  Наши 4 скилла = КАРКАС (цепочка + контракт)  │
            └─────────────────────────────────────────────┘
                 ▲                ▲                  ▲
        ПОСТАВЩИКИ          ВАЛИДАТОРЫ          ИСТОЧНИКИ/ОРКЕСТРАЦИЯ
        контента           качества            данных
```

| Роль | Что делает | Примеры |
|------|------------|---------|
| **Поставщик контента** | генерит «начинку» этапа | brainstorm, competitive-brief, write-spec, engineering:architecture |
| **Валидатор** | проверяет артефакт на стыке | code-review, verify, security-review, audit-current-tracking |
| **Источник / оркестратор** | подаёт данные и запускает цепочку | MCP (Linear, Gmail, Airtable…), Workflow, loop, skill-creator |

---

## Привязка к горизонтам роадмапа

| Горизонт | Подключённая инфраструктура / скиллы |
|----------|--------------------------------------|
| **Г1 — оркестрация** | `Workflow`, `loop`, `schedule`, MCP `scheduled-tasks`; сборка — `skill-creator`, `create-cowork-plugin` |
| **Г2 — MCP из источников** | Linear, Asana, Airtable, Supabase/DWH, Google Drive, Vercel → вход для ①–③; `mcp-registry` для поиска коннекторов |
| **Г3 — переписка** | Gmail MCP, Calendar MCP, nimble:`meeting-prep`, поиск по транскриптам сессий → извлечение требований/ADR |
| **Г4 — доработка продукта** | product-management:`metrics-review`; product-tracking:`instrument-new-feature`/`audit`; data:`analyze`/`statistical-analysis`; engineering:`incident-response`; small-business:`customer-pulse` |

---

## Сводная таблица: этап → поставщики → валидаторы → источники

| Наш скилл | Поставщики контента | Валидаторы | Источники/MCP |
|-----------|--------------------|-----------|----------------|
| ① product-vision | pm:brainstorm, competitive-brief, synthesize-research; design:user-research; bigdata:risk-assessment | — | Drive, Gmail (боли из переписки) |
| ② business-architecture | pm:write-spec, roadmap-update; product-tracking:model-product, business-case | — | Linear/Asana (бэклог), Airtable/DWH |
| ③ it-architecture | engineering:architecture, system-design, tech-debt; mcp-builder | testing-strategy | репозитории, Supabase, Vercel, CMDB |
| ④ validator | — | code-review, verify, security-review, validate-data, audit-current-tracking | BI/телеметрия, metrics-review |

---

## Главный вывод

- Наши скиллы **не дублируют** плагины — они добавляют связность: базовый контракт, `source`/provenance, `covers` для ИТ/mapping, gate и сквозную трассировку, которых у разрозненных плагин-скиллов нет.
- Плагины — готовые «движки» внутри этапов (шаг «видение» → `pm:brainstorm`; «ADR» → `engineering:architecture`).
- Для риск-продукта доменные `bigdata-com:risk-assessment` и `scenario-analysis` кормят ① (видение) и ④ (валидация сценариев).

## Что НЕ входит

- Реализация скиллов, обвязки и MCP-вызовов (создаётся отдельно, по ТЗ скиллов и роадмапу).
- Привязка к конкретной отрасли — базовая версия универсальна.
