# Пример выхода (IT Architecture)

Эталонный сквозной прогон на продукте «оценка рисков контрагента». Вход — выход примера из
`business-architecture`: capability `CAP-001` (сбор данных контрагента), `CAP-002` (расчёт
риск-скоринга), `CAP-003` (объяснение решения), `CAP-004` (мониторинг портфеля); требования
`REQ-001` (оценка ≤ 5 мин для 95% заявок, nfr/latency), `REQ-002` (каждое решение с объяснением,
func), `REQ-003` (≥ 70% полей авто-заполняются, func); принцип видения `PRIN-001` (объяснимость
обязательна). Структуру и поля бери из [templates.md](templates.md) и [contract.md](contract.md).
Прогон проходит self-check из [SKILL.md](SKILL.md): каждая `CAP-*` покрыта или помечена gap,
NFR отражены в infra-слое, есть ADR и обязательный блок обратной связи наверх.

```
## Application architecture
| id      | type        | Сервис                  | trace_up | source               | covers (CAP/REQ)  | назначение                            | status | owner | version |
|---------|-------------|-------------------------|----------|----------------------|-------------------|---------------------------------------|--------|-------|---------|
| APP-001 | application | Ingestion-сервис        | CAP-001  | architecture workshop | CAP-001, REQ-003  | сбор и авто-заполнение данных контрагента | draft  | CTO   | v0.1    |
| APP-002 | application | Scoring engine          | CAP-002  | architecture workshop | CAP-002, REQ-001  | расчёт риск-скоринга                   | draft  | CTO   | v0.1    |
| APP-003 | application | Explainability-модуль    | CAP-003  | architecture workshop | CAP-003, REQ-002  | генерация объяснимого обоснования (SHAP) | draft  | CTO   | v0.1    |
| APP-004 | application | Monitoring & alerting   | CAP-004  | architecture workshop | CAP-004           | непрерывный мониторинг портфеля и алерты | draft  | CTO   | v0.1    |

## Data architecture
| id       | type | Сущность/хранилище   | trace_up | source | data_sources              | covers (CAP/REQ) | назначение                       | status | owner      | version |
|----------|------|----------------------|----------|--------|---------------------------|------------------|----------------------------------|--------|------------|---------|
| DATA-001 | data | Профиль контрагента  | CAP-001  | CMDB   | внешние реестры (API)     | CAP-001, REQ-003 | хранение собранных данных         | draft  | Data owner | v0.1    |
| DATA-002 | data | Model-store скоринга  | CAP-002  | репозиторий моделей | feature store, БД | CAP-002          | модели и фичи скоринга            | draft  | Data owner | v0.1    |

## Technology / Infra (+ NFR)
| id        | type  | Компонент              | trace_up | source    | covers (CAP/REQ) | NFR (latency/безопасность/масштаб)        | status | owner | version |
|-----------|-------|------------------------|----------|-----------|------------------|-------------------------------------------|--------|-------|---------|
| INFRA-001 | infra | Online-inference кластер | REQ-001  | техрадар  | REQ-001          | latency ≤ 5 мин для 95% заявок            | draft  | CTO   | v0.1    |
| INFRA-002 | infra | Event-streaming (шина)  | CAP-004  | техрадар  | CAP-004          | near-real-time, масштаб по объёму событий | draft  | CTO   | v0.1    |

## Capability → application mapping
| id     | type    | CAP/REQ | Реализация                                | trace_up | source               | covers           | статус   | action                          |
|--------|---------|---------|-------------------------------------------|----------|----------------------|------------------|----------|---------------------------------|
| MAP-001| mapping | CAP-001 | APP-001 Ingestion + DATA-001 профиль      | CAP-001  | architecture workshop | CAP-001, REQ-003 | covered  | —                               |
| MAP-002| mapping | CAP-002 | APP-002 Scoring + DATA-002 model-store + INFRA-001 | CAP-002 | architecture workshop | CAP-002, REQ-001 | covered  | —                               |
| MAP-003| mapping | CAP-003 | APP-003 Explainability (SHAP)             | CAP-003  | architecture workshop | CAP-003, REQ-002 | covered  | —                               |
| MAP-004| mapping | CAP-004 | APP-004 Monitoring + INFRA-002            | CAP-004  | architecture workshop | CAP-004          | covered  | —                               |

## ADR (ключевые решения)
| id     | type | Решение                              | trace_up | source               | Альтернативы      | Обоснование                          | Последствия                                          | status | owner | version |
|--------|------|--------------------------------------|----------|----------------------|-------------------|--------------------------------------|------------------------------------------------------|--------|-------|---------|
| ADR-001| adr  | Интерпретируемая/гибридная модель    | PRIN-001 | architecture workshop | чистая нейросеть  | требование PRIN-001 (объяснимость)    | ниже риск регуляторного отказа, возможна потеря точности | draft  | CTO   | v0.1    |

## Roadmap и оценка
| id    | type         | этап/решение                        | trace_up | source | covers          | оценка/стоимость | риск                  | status | owner | version |
|-------|--------------|-------------------------------------|----------|--------|-----------------|------------------|-----------------------|--------|-------|---------|
| RM-001| roadmap_item | Этап 1: ingestion + базовый скоринг | CAP-001, CAP-002 | план | CAP-001, CAP-002 | <оценка>         | доступность внешних API | draft  | CTO   | v0.1    |
| RM-002| roadmap_item | Этап 2: explainability + мониторинг | CAP-003, CAP-004 | план | CAP-003, CAP-004 | <оценка>         | сложность объяснимости | draft  | CTO   | v0.1    |

## Обратная связь наверх (enablement / ограничения)
| id    | type     | text                                       | trace_up | source               | target   | recommendation                                              | severity | status | owner | version |
|-------|----------|--------------------------------------------|----------|----------------------|----------|-------------------------------------------------------------|----------|--------|-------|---------|
| FB-001| feedback | Чистая нейросеть точнее, но необъяснима      | ADR-001  | architecture workshop | PRIN-001 | подтвердить приоритет объяснимости над максимальной точностью | warning  | open   | CRO   | v0.1    |

## Допущения и открытые вопросы
| id         | type       | text                                                                                  | trace_up | source                  | severity | status | owner      | version |
|------------|------------|---------------------------------------------------------------------------------------|----------|-------------------------|----------|--------|------------|---------|
| ASSUMP-001 | assumption | Внешние реестры доступны по API — уверенность <60%> — проверка: PoC интеграции          | CAP-001  | интеграционная гипотеза | warning  | open   | Data owner | v0.1    |
| Q-001      | question   | Целевой объём заявок в пике не подтверждён — влияет на масштаб INFRA-001/INFRA-002      | REQ-001  | нагрузочные требования   | blocker  | open   | CTO        | v0.1    |
```
