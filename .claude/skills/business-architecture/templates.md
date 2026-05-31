# Шаблоны разделов выхода (Business Architecture)

Заполняй разделы строго в этом порядке — это единственная каноническая структура выхода.
Поля контракта — см. [contract.md](contract.md). Под каждым разделом в скобках указан тип
элемента и префикс `id`. Идентификаторы нумеруются тремя цифрами (`-001`, `-002`, …).

```
## Capability map                                   (capability · CAP-00X)
| id | type | Capability | trace_up | source | priority | KPI | status | owner | version |
|----|------|------------|----------|--------|----------|-----|--------|-------|---------|
| CAP-001 | capability | {что бизнес должен уметь} | {GOAL-/MET-/PRIN-/SEG-*} | {as-is/оргструктура/регуляторика} | Must/Should/Could/Won't | {≥1 KPI} | draft | {owner} | v0.1 |

## Процессы to-be / ценностные потоки               (process · PROC-00X)
| id | type | process | trace_up | source | status | owner | version |
|----|------|---------|----------|--------|--------|-------|---------|
| PROC-001 | process | {шаг → шаг → шаг → результат} | {CAP-*} | {воркшоп/as-is} | draft | {owner} | v0.1 |

## Требования к capability                           (requirement · REQ-00X)
| id | type | Capability | Требование | trace_up | source | тип (func/nfr) | nfr | измеримость | priority | status | owner | version |
|----|------|------------|------------|----------|--------|----------------|-----|-------------|----------|--------|-------|---------|
| REQ-001 | requirement | {CAP-*} | {измеримое требование} | {CAP-*/PRIN-*} | {источник} | func/nfr | {класс nfr или —} | {величина/единица} | Must/Should/Could/Won't | draft | {owner} | v0.1 |

## KPI / метрики процессов                           (kpi · KPI-00X)
| id | type | KPI | trace_up | source | текущее | целевое | срок | status | owner | version |
|----|------|-----|----------|--------|---------|---------|------|--------|-------|---------|
| KPI-001 | kpi | {показатель} | {CAP-*/MET-*} | {аналитика as-is} | {<unknown>/число} | {целевое} | {срок} | draft | {owner} | v0.1 |

## RACI (роли и ответственность)                     (raci · RACI-00X)
| id | type | Capability | trace_up | source | R | A | C | I | status | owner | version |
|----|------|------------|----------|--------|---|---|---|---|--------|-------|---------|
| RACI-001 | raci | {CAP-*} | {CAP-*} | {оргструктура} | {Responsible} | {Accountable} | {Consulted} | {Informed} | draft | {owner=A} | v0.1 |

## Допущения и открытые вопросы                      (assumption / question)
| id | type | text | trace_up | source | severity | status | owner | version |
|----|------|------|----------|--------|----------|--------|-------|---------|
| A-001 | assumption | {допущение} — уверенность <XX%> — проверка: {интервью/PoC/анализ as-is/Legal review} | {CAP-*/REQ-*} | {source} | warning | open | {owner} | v0.1 |
| Q-001 | question | {что нужно уточнить} | {CAP-*/Vision} | {source} | blocker | open | {owner} | v0.1 |
```
