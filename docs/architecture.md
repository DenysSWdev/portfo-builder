# Architecture

## Overview

Three triggers (GitHub, the inline editor, the chat agent) reduce to one action: **change the portfolio data in the user's repo through a PR**. After that the path is always the same: build → preview → merge → production.

```
GitHub events ─┐
Editor ────────┼─► Hono API / queue ─► data change ─► PR ─► Astro build ─► R2 (preview) ─► merge ─► R2 (prod)
Chat agent ────┘                     └─► post drafts (D1) ─► dashboard / Chrome extension
```

## Components

| Component | Package | Runtime | Responsibility |
|---|---|---|---|
| Templates | `packages/templates` | Astro | Render the site from data. Every editable element has `data-field` (see `data-model.md`). |
| Core | `packages/core` | any | Zod schemas, GitHub client, LLM layer, sanitization, card and post generators. |
| App | `apps/app` | Cloudflare Workers | Astro SSR (dashboard, editor), Hono API, webhook handling, queue consumer. |
| Site server | `apps/site-server` | Cloudflare Workers | Multi-tenant serving of sites from R2 by hostname. |
| Build runner | see open questions | — | `astro build` for one user's data, upload of the output to R2. |
| Extension | `apps/extension` | Chrome MV3 | Side Panel with post drafts. |

Cloudflare infrastructure: Workers, Queues (generation and build jobs), D1 (users, settings, post drafts, job state), R2 (built sites), Cloudflare for SaaS (custom domains), Cron Triggers.

## Site hosting (ADR 0001)

- One `site-server` Worker serves every site. It finds the user by `Host` (D1, cached in KV) and serves files from R2.
- R2 layout:
  - `sites/<userId>/prod/…` — production.
  - `sites/<userId>/preview/<prNumber>/…` — PR preview.
- Addresses: prod `username.<domain>`; preview `pr-<n>--username.preview.<domain>` (wildcard DNS on our own zone); custom domains through Cloudflare for SaaS custom hostnames.
- Responses are cached with the Cache API; a deploy invalidates the user's cache.
- We do **not** create a separate Cloudflare Pages project per user: an account has a soft limit on project count and limits on builds.

## Pipeline: new or updated project

1. **Event detection** (see open questions for the mechanism). Events of interest: repo became public, the `portfolio` topic was added, README changed, new Release.
2. **Data collection** (`core/github`): README, `/languages`, topics, homepage, stars, last push date, screenshots from the repo, release notes (for a release).
3. **Generation** (`core/llm`): a project card and, if the event warrants it, a post draft. The model returns JSON through tool use.
4. **Validation**: the Zod schema from `data-model.md`. Invalid → one retry with the validation error in the prompt → invalid again → the job is `failed` and the user sees the error in the dashboard.
5. **Merge with existing data**: fields in `editedFields` are taken from the current file, not from generation.
6. **PR** to the data repo: branch `portfolio-bot/<slug>-<timestamp>`, one project file + assets. The PR description says in plain language what changed.
7. **Build and preview**: the build runner builds the site from the PR branch into `preview/<prNumber>`. The link goes into a PR comment and the dashboard.
8. **Merge**: manual, or auto-merge if enabled and all checks (schema, build) are green. After merge, a build into `prod`.

Posts are stored in D1 as drafts linked to the project and event; they never go into the repo.

## LLM (ADR 0003)

- Provider: Anthropic. All access goes through an interface in `core/llm` (one function per task: `generateProjectCard`, `generatePost`, `chatEdit`), so changing provider means changing the implementation, not the call sites.
- Models are in configuration, not in code: a cheap Haiku-class model for cards and posts; Sonnet for the chat agent. Check model identifiers against current Anthropic documentation before first use.
- System prompts and style instructions form a stable prefix that is cached (prompt caching).
- Per-user daily generation limits are stored in D1; exceeding them defers the job.
- Security: repo content goes into the prompt as data in a clearly delimited block; at the generation step the model has no tools with write access; output is validated and sanitized.

## Inline editor

- Lives in `apps/app` (`/edit`), not on the public site. `?edit` on the user's site only redirects.
- Renders the same `packages/templates` components with an `editable` flag: elements with `data-field` get `contenteditable`, images get a replace button.
- Paste into `contenteditable` is cleaned to plain text.
- Save: the client sends a `{field: value}` diff → the API applies it to the data and adds the field to `editedFields` → commit to a branch → PR → preview.
- Images: upload → resize to ≤2000px and compress to WebP → commit to `assets/` in the data repo.

## Chrome extension

- MV3, Side Panel API. Auth with an app session token (sign in through an app page).
- v1: list of drafts, copy, edit, "published". No interaction with social network DOMs.

## Open questions

1. **Build runner.** Workers cannot run `astro build`. Options: GitHub Actions in a service repo (triggered via `workflow_dispatch` from the API); Cloudflare Containers; a custom build without the Astro CLI. Needs a decision before M3 → ADR.
2. **Event detection and GitHub App permissions.** Webhooks only arrive from repos where the App is installed, and App permissions are the same for all of them. Installing on every repo with `contents: write` is excessive. Recommended: install the App **only on the data repo** (write access), and poll the user's public repos with a Cron Trigger through the API (public data, minutes of latency). Alternative: webhooks from all repos with read-only permissions plus a separate write mechanism. Needs a decision before M4 → ADR.
3. **Choosing projects to show:** only the `portfolio` topic, only a dashboard checkbox, or both (topic as the automatic way, checkbox as the manual one).
4. **Creating the data repo:** the service creates it on the user's behalf (needs a user token with the right permissions), or the user creates it from a template repo and selects it when installing the App.
