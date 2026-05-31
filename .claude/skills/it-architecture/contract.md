# Handoff-контракт (IT Architecture)

Каждый элемент выхода оформляется как запись базового handoff-контракта цепочки SDD.
Корневого `ROOT` здесь нет: элементы трассируются вверх на конкретные `id` из выхода
`business-architecture` (`CAP-*`, `REQ-*`) и `product-vision` (`PRIN-*`).

| Поле | Назначение |
|------|------------|
| `id` | Уникальный идентификатор (`APP-001`, `DATA-001`, `INFRA-001`, `MAP-001`, `ADR-001`, `RM-001`, `FB-001`) |
| `type` | application / data / infra / mapping / adr / roadmap_item / feedback / assumption / question |
| `text` | Содержание элемента |
| `trace_up` | Внутренняя связь вверх (см. правила ниже) |
| `source` | Внешний источник/provenance: репозиторий, CMDB, стандарт, техрадар, архитектурный воркшоп |
| `covers` | Какие `CAP-*`/`REQ-*` закрывает компонент (только для application/data/infra/mapping) |
| `data_sources` | Источники данных (только для data: внешний API, БД, событие, файл) |
| `nfr` | Покрываемые нефункциональные требования (latency / throughput / availability / security / compliance) |
| `target` | На какой `CAP-*`/`REQ-*`/`PRIN-*` направлена обратная связь (только для feedback) |
| `recommendation` | Что предложить наверх (только для feedback) |
| `status` | draft / approved (для feedback/assumption/question — open / resolved) |
| `owner` | Владелец решения/компонента |
| `version` | Версия артефакта |

## Правила трассировки

- `application` / `data` / `infra` → `trace_up` на `CAP-*` или `REQ-*`, которые компонент реализует.
  Дополнительно заполняется `covers` — полный перечень покрываемых `CAP-*`/`REQ-*`.
- `mapping` → `trace_up` на покрываемую `CAP-*` (или `REQ-*`); `covers` дублирует покрытие,
  `статус` = `covered` / `gap`. Для `gap` обязателен `action`.
- `adr` → `trace_up` на `PRIN-*`, если решение реализует принцип видения; иначе — на `CAP-*`/`REQ-*`,
  к которым относится решение.
- `roadmap_item` → `trace_up` на `CAP-*`/`REQ-*`; `covers` — что закрывает этап.
- `feedback` → `trace_up` на ближайший внутренний элемент (`ADR-*`/`APP-*`/`REQ-*`);
  `target` указывает, какой элемент наверху (`CAP-*`/`REQ-*`/`PRIN-*`) предлагается скорректировать.
- `assumption` / `question` → `trace_up` на наиболее близкий элемент (`CAP-*`, `REQ-*` или ИТ-компонент).

## Правило покрытия

- Каждая `CAP-*` из выхода `business-architecture` **обязана** встречаться в `covers`
  хотя бы одного компонента (application/data/infra) или mapping-записи. Непокрытая capability —
  это **gap**: фиксируется в разделе mapping со статусом `gap` и обязательным `action`.
- Каждое требование `REQ-*` типа `nfr` должно быть отражено в infra-слое через `trace_up`/`covers`.

## Прочие правила

- `source` фиксирует происхождение данных и **не подменяет** `trace_up`.
  `trace_up` — внутренняя связь по цепочке артефактов, `source` — внешний провенанс,
  `covers` — техническое покрытие.
- `covers` заполняется только в ИТ-компонентах (application/data/infra) и mapping-записях;
  для записей Vision/Business он не требуется.
- Присвоенные здесь `id` (`APP-*`, `DATA-*`, `INFRA-*`, `ADR-*`) далее используются
  скиллом-валидатором в матрице трассируемости.
- Для записей `assumption` / `question` дополнительно указывается `severity`
  (blocker / warning / info) и `status` (open / resolved); для `assumption` — ещё уверенность
  `<XX%>` и способ проверки (см. шаблон в [templates.md](templates.md)).
- Для записей `feedback` указывается `severity` (blocker / warning / info) и `status` (open / resolved).
- Идентификаторы нумеруются тремя цифрами: `-001`, `-002`, … (единый формат во всех артефактах цепочки).
