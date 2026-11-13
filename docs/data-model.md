# Data model

The source of truth for schemas is Zod in `packages/core/schemas`. This document describes intent; if they diverge, reconcile the schema and this document first.

## User's data repo

```
portfolio.config.json     # template, theme, settings
profile.json              # profile
projects/<slug>.md        # one project card: frontmatter + markdown description
certificates/<slug>.json  # certificates
assets/
  avatar.webp
  projects/<slug>/*.webp
  certificates/*          # webp or pdf
```

The template is not stored in the repo: it lives in `packages/templates`; the repo holds only data.

### `portfolio.config.json`
| Field | Type | Description |
|---|---|---|
| `template` | string | template id from `packages/templates` |
| `theme` | `"light" \| "dark" \| "auto"` | |
| `autoMerge` | boolean | defaults to `false` |
| `language` | string | language of generated text, e.g. `"uk"`, `"en"` |

### `profile.json`
| Field | Type | Source |
|---|---|---|
| `name` | string | GitHub, editable |
| `title` | string | user (e.g. "Senior Engineer") |
| `bio` | string (markdown) | GitHub bio as the initial value |
| `avatar` | string | path in `assets/` or the GitHub avatar URL |
| `location` | string? | GitHub |
| `contacts` | `{ type, value }[]` | user (email, telegram…) |
| `socials` | `{ network, url }[]` | user + GitHub (blog, twitter_username) |
| `editedFields` | string[] | fields changed manually |

### `projects/<slug>.md` — frontmatter
| Field | Type | Description |
|---|---|---|
| `repo` | string | `owner/name` |
| `title` | string | human-readable name |
| `summary` | string | 1–2 sentences: what it is and what problem it solves |
| `stack` | string[] | technologies (from `/languages` + README analysis) |
| `demoUrl` | string? | repo homepage or a link from the README |
| `images` | string[] | paths in `assets/projects/<slug>/` |
| `featured` | boolean | show first |
| `order` | number | manual sorting |
| `stars` | number | updated by the pipeline |
| `lastActivity` | string (ISO date) | |
| `generatedAt` | string (ISO datetime) | |
| `editedFields` | string[] | fields changed manually |

The file body is an extended description in markdown (no raw HTML).

### `certificates/<slug>.json`
`title`, `issuer`, `date`, `url?`, `file?` (path in `assets/certificates/`), `editedFields`.

## The `editedFields` rule

- Any manual change (editor, chat agent on a direct instruction, a user commit) adds the field name to `editedFields`.
- Generation never changes fields in `editedFields`.
- The user can reset a field ("regenerate"): the field is removed from `editedFields`.

## `data-field` in templates

Every editable template element has a `data-field` with the path to its data:
- `profile.title`, `profile.bio`, `profile.avatar`
- `projects.<slug>.summary`, `projects.<slug>.title`
- `certificates.<slug>.title`

The editor maps `data-field` back to a file and field without parsing HTML.

## D1 (app)

| Table | Purpose |
|---|---|
| `users` | GitHub id, login, settings, limits |
| `installations` | GitHub App installation id, data repo |
| `sites` | hostname(s), deploy state, current prod commit |
| `jobs` | pipeline jobs: type, state (`queued/running/done/failed`), error, PR |
| `post_drafts` | drafts: project, event, text, network, state (`draft/published/dismissed`) |
| `llm_usage` | generation counter for limits |
