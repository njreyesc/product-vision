# Evaluations — business-architecture

Сценарии для проверки скилла по методике Anthropic (evaluation-driven development).
Прогонять на Haiku, Sonnet и Opus: что хорошо для Opus, может требовать больше деталей для Haiku.
Встроенного раннера нет — `expected_behavior` проверяется ревьюером/скриптом вручную.

## Eval 1 — базовый прогон (happy path, на входе выход product-vision)

```json
{
  "skills": ["business-architecture"],
  "query": "На входе видение продукта оценки рисков: GOAL-001 сократить цикл решения (MET-001 время оценки ≤5 мин), GOAL-002 снизить повторные ревью (MET-002 ≤10%), PRIN-001 объяснимость обязательна, SEG-001 риск-менеджер. Построй бизнес-архитектуру.",
  "files": [],
  "expected_behavior": [
    "Строит capability map; у каждой capability есть trace_up на GOAL-*/MET-*/PRIN-*/SEG-*, priority (MoSCoW) и ≥1 KPI",
    "Описывает процессы to-be с trace_up на CAP-*",
    "Требования измеримы (величина + порог + единица) и помечены func/nfr; для nfr указан класс ограничения",
    "Принцип PRIN-001 (объяснимость) отражён хотя бы в одном требовании через trace_up",
    "Заполнены RACI и раздел «Допущения и открытые вопросы»",
    "У каждого элемента заполнены id/type/trace_up/source/status/owner/version; вывод на русском, в таблицах"
  ]
}
```

## Eval 2 — неполный вход (скилл фиксирует пробелы, а не выдумывает)

```json
{
  "skills": ["business-architecture"],
  "query": "Нужна capability map для внутреннего инструмента поддержки. Точных KPI, владельцев и SLA пока нет.",
  "files": [],
  "expected_behavior": [
    "Не выдумывает KPI, SLA и владельцев — ставит <unknown>/<to validate>/<owner> или [H] гипотеза",
    "Недостающие данные выносит в «Допущения и открытые вопросы» как question/assumption с severity и status",
    "Сохраняет полный контракт полей даже при скудном входе",
    "Capability без явной цели Vision помечает question'ом с trace_up на ближайший элемент"
  ]
}
```

## Eval 3 — трассируемость вверх (нет «висячих» capability)

```json
{
  "skills": ["business-architecture"],
  "query": "Добавь capability «геймификация профиля» в архитектуру продукта оценки рисков, где её нет в целях видения.",
  "files": [],
  "expected_behavior": [
    "Не создаёт capability без trace_up на элемент Vision",
    "Сигнализирует, что для capability нет цели/принципа в product-vision (обратная связь снизу вверх)",
    "Предлагает либо отклонить capability, либо вернуться в product-vision и добавить цель",
    "Не выдаёт выдуманную цель ради формального заполнения trace_up"
  ]
}
```

## Eval 4 — измеримость требований

```json
{
  "skills": ["business-architecture"],
  "query": "Сформулируй требования к capability расчёта скоринга: «система должна работать быстро, надёжно и удобно».",
  "files": [],
  "expected_behavior": [
    "Переписывает неизмеримые требования в измеримые (например, latency ≤ N сек для X% запросов, availability ≥ 99.X%)",
    "Помечает каждое требование func/nfr; для nfr указывает класс (latency/availability/...)",
    "Где нет числовых порогов — ставит плейсхолдер <XX%>/<срок> и выносит в допущения",
    "Каждое требование имеет trace_up на CAP-* или PRIN-*"
  ]
}
```

## Eval 5 — отказ от чужой высоты (anti-scope)

```json
{
  "skills": ["business-architecture"],
  "query": "Построй бизнес-архитектуру и заодно выбери микросервисы, базу данных и конкретные API для интеграций.",
  "files": [],
  "expected_behavior": [
    "Формирует только capability map, процессы, требования, KPI и RACI",
    "Не выбирает системы, стек, БД и API — указывает, что это it-architecture",
    "Не переписывает видение и стратегию — указывает, что это product-vision",
    "Объясняет, что выход (CAP-*/REQ-*/KPI-*) передаётся дальше по цепочке SDD в it-architecture"
  ]
}
```
