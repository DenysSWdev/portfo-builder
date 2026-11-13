# Модель даних

Джерело правди для схем — Zod у `packages/core/schemas`. Цей документ описує намір; при розбіжності спершу узгодь схему і документ.

## Репо даних користувача

```
portfolio.config.json     # шаблон, тема, налаштування
profile.json              # профіль
projects/<slug>.md        # одна картка проєкту: frontmatter + опис у markdown
certificates/<slug>.json  # сертифікати
assets/
  avatar.webp
  projects/<slug>/*.webp
  certificates/*          # webp або pdf
```

Шаблон не зберігається в репо: він у `packages/templates`, репо містить лише дані.

### `portfolio.config.json`
| Поле | Тип | Опис |
|---|---|---|
| `template` | string | id шаблону з `packages/templates` |
| `theme` | `"light" \| "dark" \| "auto"` | |
| `autoMerge` | boolean | за замовчуванням `false` |
| `language` | string | мова генерованих текстів, напр. `"uk"`, `"en"` |

### `profile.json`
| Поле | Тип | Джерело |
|---|---|---|
| `name` | string | GitHub, редагується |
| `title` | string | користувач (напр. "Senior Engineer") |
| `bio` | string (markdown) | GitHub bio як стартове значення |
| `avatar` | string | шлях у `assets/` або URL аватара GitHub |
| `location` | string? | GitHub |
| `contacts` | `{ type, value }[]` | користувач (email, telegram…) |
| `socials` | `{ network, url }[]` | користувач + GitHub (blog, twitter_username) |
| `editedFields` | string[] | поля, змінені вручну |

### `projects/<slug>.md` — frontmatter
| Поле | Тип | Опис |
|---|---|---|
| `repo` | string | `owner/name` |
| `title` | string | людська назва |
| `summary` | string | 1–2 речення: що це і яку проблему вирішує |
| `stack` | string[] | технології (з `/languages` + аналіз README) |
| `demoUrl` | string? | homepage репо або посилання з README |
| `images` | string[] | шляхи в `assets/projects/<slug>/` |
| `featured` | boolean | показувати першим |
| `order` | number | ручне сортування |
| `stars` | number | оновлюється пайплайном |
| `lastActivity` | string (ISO date) | |
| `generatedAt` | string (ISO datetime) | |
| `editedFields` | string[] | поля, змінені вручну |

Тіло файлу — розширений опис у markdown (без сирого HTML).

### `certificates/<slug>.json`
`title`, `issuer`, `date`, `url?`, `file?` (шлях в `assets/certificates/`), `editedFields`.

## Правило `editedFields`

- Будь-яка ручна зміна (редактор, чат-агент за прямою вказівкою, коміт користувача) додає назву поля до `editedFields`.
- Генерація ніколи не змінює поля з `editedFields`.
- Користувач може скинути поле ("перегенерувати") — поле видаляється з `editedFields`.

## `data-field` у шаблонах

Кожен редагований елемент шаблону має `data-field` з шляхом до даних:
- `profile.title`, `profile.bio`, `profile.avatar`
- `projects.<slug>.summary`, `projects.<slug>.title`
- `certificates.<slug>.title`

Редактор відображає `data-field` назад у файл і поле без парсингу HTML.

## D1 (застосунок)

| Таблиця | Призначення |
|---|---|
| `users` | GitHub id, login, налаштування, ліміти |
| `installations` | GitHub App installation id, репо даних |
| `sites` | hostname(и), стан деплою, поточний prod-коміт |
| `jobs` | задачі пайплайну: тип, стан (`queued/running/done/failed`), помилка, PR |
| `post_drafts` | чернетки: проєкт, подія, текст, мережа, стан (`draft/published/dismissed`) |
| `llm_usage` | лічильник генерацій для лімітів |
