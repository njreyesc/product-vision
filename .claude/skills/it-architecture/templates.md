# Шаблоны разделов выхода (IT Architecture)

Заполняй разделы строго в этом порядке — это единственная каноническая структура выхода.
Поля контракта — см. [contract.md](contract.md). Под каждым разделом в скобках указан тип
элемента и префикс `id`. Идентификаторы нумеруются тремя цифрами (`-001`, `-002`, …).

```
## Application architecture                          (application · APP-00X)
| id | type | Сервис | trace_up | source | covers (CAP/REQ) | назначение | status | owner | version |
|----|------|--------|----------|--------|------------------|------------|--------|-------|---------|
| APP-001 | application | {сервис/приложение} | {CAP-*/REQ-*} | {репозиторий/воркшоп} | {CAP-*, REQ-*} | {что делает} | draft | {owner} | v0.1 |

## Data architecture                                 (data · DATA-00X)
| id | type | Сущность/хранилище | trace_up | source | data_sources | covers (CAP/REQ) | назначение | status | owner | version |
|----|------|--------------------|----------|--------|--------------|------------------|------------|--------|-------|---------|
| DATA-001 | data | {сущность/хранилище} | {CAP-*/REQ-*} | {CMDB/воркшоп} | {внешний API/БД/событие} | {CAP-*, REQ-*} | {что хранит} | draft | {owner} | v0.1 |

## Technology / Infra (+ NFR)                         (infra · INFRA-00X)
| id | type | Компонент | trace_up | source | covers (CAP/REQ) | NFR (latency/безопасность/масштаб) | status | owner | version |
|----|------|-----------|----------|--------|------------------|------------------------------------|--------|-------|---------|
| INFRA-001 | infra | {компонент/стек/API} | {REQ-*/CAP-*} | {техрадар/стандарт} | {REQ-*} | {latency ≤ N / шифрование / X RPS} | draft | {owner} | v0.1 |

## Capability → application mapping                   (mapping · MAP-00X)
| id | type | CAP/REQ | Реализация (APP/DATA/INFRA) | trace_up | source | covers | статус (covered/gap) | action |
|----|------|---------|-----------------------------|----------|--------|--------|----------------------|--------|
| MAP-001 | mapping | {CAP-*} | {APP-*/DATA-*/INFRA-*} | {CAP-*} | {воркшоп} | {CAP-*, REQ-*} | covered/gap | {— или что сделать для gap} |

## ADR (ключевые решения)                             (adr · ADR-00X)
| id | type | Решение | trace_up | source | Альтернативы | Обоснование | Последствия | status | owner | version |
|----|------|---------|----------|--------|--------------|-------------|-------------|--------|-------|---------|
| ADR-001 | adr | {выбранное решение} | {PRIN-*/CAP-*/REQ-*} | {воркшоп} | {что отвергли} | {почему} | {плюсы/минусы/риски} | draft | {owner} | v0.1 |

## Roadmap и оценка                                   (roadmap_item · RM-00X)
| id | type | этап/решение | trace_up | source | covers | оценка/стоимость | риск | status | owner | version |
|----|------|--------------|----------|--------|--------|------------------|------|--------|-------|---------|
| RM-001 | roadmap_item | {этап} | {CAP-*/REQ-*} | {план} | {CAP-*, REQ-*} | {<оценка>/<стоимость>} | {риск} | draft | {owner} | v0.1 |

## Обратная связь наверх (enablement / ограничения)   (feedback · FB-00X)
| id | type | text | trace_up | source | target | recommendation | severity | status | owner | version |
|----|------|------|----------|--------|--------|----------------|----------|--------|-------|---------|
| FB-001 | feedback | {что нереализуемо/дорого/долго или конфликт} | {ADR-*/APP-*/REQ-*} | {воркшоп} | {CAP-*/REQ-*/PRIN-*} | {что предложить наверх} | warning | open | {owner} | v0.1 |

## Допущения и открытые вопросы                       (assumption / question)
| id | type | text | trace_up | source | severity | status | owner | version |
|----|------|------|----------|--------|----------|--------|-------|---------|
| ASSUMP-001 | assumption | {допущение} — уверенность <XX%> — проверка: {PoC/нагрузочный тест/security review/запрос в CMDB} | {CAP-*/REQ-*} | {source} | warning | open | {owner} | v0.1 |
| Q-001 | question | {что нужно уточнить} | {CAP-*/REQ-*/компонент} | {source} | blocker | open | {owner} | v0.1 |
```
