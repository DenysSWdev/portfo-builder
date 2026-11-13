# M1: Генератор — username → статичний сайт

## Мета
Довести ядро ідеї без авторизації, LLM і хостингу: з публічних даних GitHub отримати дані портфоліо і зібрати з них сайт.

## Обсяг
- Каркас монорепо: pnpm workspaces, TS strict, Vitest, лінтер, пакети з `CLAUDE.md` (поки порожні `apps/*`).
- `packages/core/schemas`: Zod-схеми з `docs/data-model.md`.
- `packages/core/github`: клієнт публічного API (профіль, публічні репо, README, languages, topics). Токен опційний через env для лімітів.
- CLI `pnpm generate <username> --out <dir>`: створює репо даних у теці — `profile.json`, `portfolio.config.json`, `projects/<slug>.md` для репо з topic `portfolio` (якщо таких немає — топ-6 за зірками, без форків і архівованих). `summary` поки — опис репо з GitHub.
- `packages/templates`: один шаблон з `data-field` на редагованих елементах; `pnpm build-site <dataDir>` збирає статику Astro.

## Definition of done
- [ ] `pnpm generate <реальний username>` і `pnpm build-site` дають сайт, що відкривається локально і показує профіль та проєкти.
- [ ] Згенеровані дані проходять Zod-схеми; тест на кожну схему (валідний і невалідний приклад).
- [ ] Тест GitHub-клієнта на записаних відповідях API (без мережі).
- [ ] Форки й архівовані репо не потрапляють у вибірку; тест.
- [ ] Шаблон не використовує сирий HTML з даних; markdown рендериться з санітизацією.
- [ ] Кожен редагований елемент шаблону має `data-field` за `data-model.md`.
- [ ] Розділ "Стек" у `CLAUDE.md` відповідає фактичній структурі.
