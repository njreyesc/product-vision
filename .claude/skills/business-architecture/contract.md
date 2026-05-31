# Handoff-контракт (Business Architecture)

Каждый элемент выхода оформляется как запись базового handoff-контракта цепочки SDD.
В отличие от `product-vision`, здесь корневого `ROOT` уже нет: элементы трассируются вверх
на конкретные `id` из выхода `product-vision`.

| Поле | Назначение |
|------|------------|
| `id` | Уникальный идентификатор (`CAP-001`, `PROC-001`, `REQ-001`, `KPI-001`, `RACI-001`) |
| `type` | capability / process / requirement / kpi / role / raci / assumption / question |
| `text` | Содержание элемента |
| `trace_up` | Внутренняя связь вверх (см. правила ниже) |
| `source` | Внешний источник/provenance: as-is процесс, оргструктура, регуляторика, бэклог, интервью |
| `priority` | MoSCoW: Must / Should / Could / Won't (для capability и requirement) |
| `impact` | Тип воздействия на capability: use / improve / create / integrate / enabler (для capability) |
| `gap` | Разрыв as-is → to-be: что нужно закрыть (для capability). «Что уже есть сейчас» фиксируется в `source` |
| `nfr` | Класс нефункционального ограничения, если требование — `nfr` (latency, throughput, availability, security, compliance) |
| `status` | draft / approved |
| `owner` | Владелец (RACI: Accountable) |
| `version` | Версия артефакта |

## Правила трассировки

- `capability` → `trace_up` на элемент Vision: `GOAL-*`, `MET-*`, `PRIN-*` или `SEG-*`.
  Capability без `trace_up` запрещена. Каждой capability задаются `impact` и `gap`
  (повествовательную проработку см. в [capability-impact-map.md](capability-impact-map.md)).
- `requirement` → `trace_up` на родительскую `CAP-*` (или напрямую на `PRIN-*`, если требование
  реализует принцип из видения).
- `process` / `kpi` / `raci` → `trace_up` на `CAP-*` (KPI может ссылаться и на `MET-*`,
  если детализирует North Star метрику).
- `assumption` / `question` → `trace_up` на наиболее близкий элемент (`CAP-*`, `REQ-*`
  или элемент Vision).

## Прочие правила

- `source` фиксирует происхождение данных и **не подменяет** `trace_up`.
  `trace_up` — внутренняя связь по цепочке артефактов, `source` — внешний провенанс.
- Каждый принцип из видения (`PRIN-*`) должен быть отражён хотя бы в одном `requirement`
  через `trace_up`.
- Присвоенные здесь `id` (`CAP-*`, `REQ-*`, `KPI-*`) далее используются в `it-architecture`
  как значения `trace_up` и в матрице трассируемости валидатора.
- Для записей `assumption` / `question` дополнительно указывается `severity`
  (blocker / warning / info) и `status` (open / resolved); для `assumption` — ещё уверенность
  `<XX%>` и способ проверки (см. шаблон в [templates.md](templates.md)).
- Идентификаторы нумеруются тремя цифрами: `-001`, `-002`, … (единый формат во всех артефактах
  цепочки).
