# Плейбук: добавление/развитие фичи по SDD через эти скиллы

**Дата:** 2026-05-31
**Контекст продукта (пример):** платформа оценки рисков (risk assessment).
**Важно:** это рабочий плейбук применения скиллов. Сами скиллы по нему пока **не создаются**.

Описывает, как разрабатывать фичу по Spec-Driven Development, используя цепочку
`product-vision → business-architecture → it-architecture → architecture-validator`.
Принцип: для фичи делается **delta-проход** (инкремент), а не полный прогон с нуля.

---

## Соответствие SDD

```
SDD-стадия          Наш скилл                Что делаешь для фичи
──────────────────────────────────────────────────────────────────
Constitution    →   принципы в vision         фича не нарушает инварианты
Specify         →   product-vision            delta: к какой GOAL/PRIN фича (trace_up)
                    business-architecture     новые/изменённые CAP + требования
Plan            →   it-architecture           как реализуем + ADR
Gate            →   architecture-validator    трассировка Vision→CAP→компонент, нет gap
Tasks/Implement →   engineering-скиллы + код  дробим на задачи и реализуем
```

---

## Шаг 0. Оценка высоты фичи (altitude-check)

Перед всем — определи **уровень** фичи (режим altitude-checker в `architecture-validator`).
Глубина SDD-прохода пропорциональна высоте.

| Уровень | Что это | Что прогонять |
|---------|---------|---------------|
| 0 — Код | рефакторинг/баг-фикс, поведение то же | сразу Tasks → Implement |
| 1 — Requirement | изменение в рамках существующей `CAP-*` | Шаг 2 (REQ) + Шаг 4 (gate) → код |
| 2 — Capability | новая способность/модуль/процесс | полный delta 0→5 |
| 3 — Vision | новая цель/метрика/сегмент | + пересмотр `product-vision` |
| 4 — Principle | трогает/нарушает `PRIN-*`, регуляторику | + `constitution`, эскалация |

**Trace-тест (итог = максимум):** к чему `trace_up`? сколько новых `id`? трогает `PRIN`? кто approve?
**Скрытая высота** (поднять уровень): новый источник данных; «временно обойдём»; меняется доступ к решениям; регуляторно-чувствительно.

---

## Шаг 1. Привязка к видению (Constitution / Specify)

- Определи `trace_up`: к какой `GOAL-*` или `PRIN-*` относится фича.
- Нет привязки → red flag: фича вне стратегии, обсуди с владельцем Vision.
- Меняет принцип → эскалация на `constitution`.

## Шаг 2. `business-architecture` (delta) — Specify

Выдай delta-артефакт:
- новый `CAP-*` (если новая способность) **или** новый `REQ-*` к существующей capability;
- обязательный `trace_up`, `priority` (MoSCoW), KPI.

## Шаг 3. `it-architecture` (delta) — Plan

- Спроектируй только затронутый кусок: какие сервисы/данные меняются (`covers`), какие NFR.
- Заведи `ADR-*`, если выбор неочевиден.
- Блок обратной связи: дорого/нереализуемо → возврат на Шаг 2.

## Шаг 4. `architecture-validator` (gate)

- Проверь по новым `id`: фича прослеживается `Vision → CAP → компонент`?
- Нет orphan-компонента? покрыты NFR? не нарушен `PRIN`?
- `pass` → дальше; `fail` → возврат на нужный шаг.

## Шаг 5. Tasks → Implement (нижний SDD)

- Дроби на задачи (трекер: `create_tasks`).
- Плагин-скиллы: `engineering:architecture` (доформить ADR), `testing-strategy`.
- Нижний gate: `code-review` / `verify` / `security-review`.

---

## Дерево «когда упрощать»

```
Баг-фикс/косметика ───────────────▶ Шаг 5 (код)
Малая фича (в рамках CAP) ─────────▶ Шаг 2 (REQ) + Шаг 4 (gate) ▶ Шаг 5
Новая capability ─────────────────▶ Шаги 0→5 полностью
Меняет принцип/стратегию ─────────▶ + constitution + пересмотр Vision
```

Правило: **не переусердствуй** — глубина прохода = высота фичи. Баг не трогает Vision, не гоняй верх.

---

## Шаблон feature-delta артефакта

```
# Feature: {название}
Уровень (altitude): {0–4}   |   Скрытая высота: {да/нет}

## Vision-привязка
trace_up: {GOAL-XX / PRIN-XX}

## Business-arch delta
- {CAP-новый или REQ-новый}  trace_up: {GOAL-XX}  priority: {Must/Should}  KPI: {…}

## IT-arch delta
- {APP/DATA-новый}  covers: {CAP-XX}
- ADR-XX: {решение}  |  альтернативы  |  обоснование
- обратная связь: {ограничения реализуемости, если есть}

## Validator (gate)
трассировка: {GOAL → CAP → компонент} = {OK/GAP}
вердикт: {PASS / FAIL}

## Tasks
- {задача 1}, {задача 2}, …
```

---

## Пример: фича «учёт ESG-риска контрагента»

```
Шаг 0: altitude — новой CAP не было → Уровень 2 (полный delta)
Шаг 1: trace_up → GOAL-01 (точность оценки). PRIN не нарушены. ✅
Шаг 2 (business-arch delta):
   CAP-05 «ESG-скоринг»  trace_up: GOAL-01  priority: Should  KPI: покрытие ESG-данными
   REQ-07 «ESG-фактор входит в риск-рейтинг»  тип: func
Шаг 3 (it-arch delta):
   APP-09 ESG-data-сервис  covers: CAP-05
   ADR-03 «ESG: внешний провайдер vs свой сбор» → провайдер
   обратная связь: данные платные → подтвердить бюджет
Шаг 4 (validator): GOAL-01 → CAP-05 → APP-09 = OK; NFR покрыт; gap нет → PASS
Шаг 5: задачи в трекере → код → code-review/verify
```

---

## Что даёт связка (а не просто «накодить»)

- **Трассируемость:** каждая фича доказуемо привязана к цели — нет «фич ради фич».
- **Gate ловит scope creep:** orphan-компонент = фича притащила лишнее.
- **Delta вместо переписывания:** меняешь только затронутые `id`.
- **Обратная связь зашита:** нереализуемость всплывает на Plan, а не в проде.

## Связь с другими документами

- Скиллы: [task-skill-1..4](task-skill-1-product-vision.md)
- Валидатор + altitude-checker: [task-skill-4-architecture-validator.md](task-skill-4-architecture-validator.md)
- Роадмап системы: [task-skills-system-roadmap.md](task-skills-system-roadmap.md)
- Карта плагинов/MCP: [task-skills-integration-map.md](task-skills-integration-map.md)

## Что НЕ входит

- Реализация скиллов и обвязки (создаётся отдельно, по ТЗ).
- Привязка к конкретной отрасли — базовая версия универсальна.
