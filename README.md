# product-vision — комплект ТЗ на систему скиллов

Набор технических заданий на систему скиллов, моделирующую сквозную цепочку
**Product Vision → Бизнес-архитектура → ИТ-архитектура → Валидатор** с принципами
Spec-Driven Development (SDD): единый handoff-контракт, трассируемость, gate-проверка.

> Контекст-пример во всех документах — платформа оценки рисков (risk assessment).
> Это ТЗ: сами скиллы по ним не создаются.

## Состав

| Файл | Назначение |
|------|------------|
| `task-skill-1-product-vision.md` | Скилл «Product Vision» (зачем) |
| `task-skill-2-business-architecture.md` | Скилл «Бизнес-архитектура» (что) |
| `task-skill-3-it-architecture.md` | Скилл «ИТ-архитектура» (как) |
| `task-skill-4-architecture-validator.md` | Скилл-валидатор (gate) + altitude-checker |
| `task-skills-feature-workflow.md` | Плейбук добавления фичи по SDD (delta-проход) |
| `task-skills-roadmap.md` | Роадмап по оси данных/источников (горизонты Г0–Г5) |
| `task-skills-system-roadmap.md` | Роадмап зрелости системы (релизы R0–R5) |
| `task-skills-integration-map.md` | Карта связи с плагин-скиллами и MCP |

## Порядок чтения

1. Скиллы 1→4 (цепочка и контракт).
2. `feature-workflow` (как применять для фичи).
3. Роадмапы (куда система развивается).
4. `integration-map` (как стыкуется с существующими скиллами/MCP).
