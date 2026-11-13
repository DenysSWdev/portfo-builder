# M1: Generator — username → static site

## Goal
Prove the core idea without auth, LLM or hosting: get portfolio data from public GitHub data and build a site from it.

## Scope
- Monorepo skeleton: pnpm workspaces, TS strict, Vitest, a linter, the packages from `CLAUDE.md` (`apps/*` empty for now).
- `packages/core/schemas`: Zod schemas from `docs/data-model.md`.
- `packages/core/github`: public API client (profile, public repos, README, languages, topics). Optional token via env for rate limits.
- CLI `pnpm generate <username> --out <dir>`: creates a data repo in the folder — `profile.json`, `portfolio.config.json`, `projects/<slug>.md` for repos with the `portfolio` topic (if there are none, the top 6 by stars, excluding forks and archived repos). `summary` is the GitHub repo description for now.
- `packages/templates`: one template with `data-field` on editable elements; `pnpm build-site <dataDir>` builds the Astro static site.

## Definition of done
- [ ] `pnpm generate <real username>` and `pnpm build-site` produce a site that opens locally and shows the profile and projects.
- [ ] Generated data passes the Zod schemas; a test for each schema (a valid and an invalid example).
- [ ] GitHub client test on recorded API responses (no network).
- [ ] Forks and archived repos are excluded from selection; test.
- [ ] The template uses no raw HTML from data; markdown is rendered with sanitization.
- [ ] Every editable template element has `data-field` per `data-model.md`.
- [ ] The "Stack" section of `CLAUDE.md` matches the actual structure.
