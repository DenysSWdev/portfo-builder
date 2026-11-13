# Portfolio from GitHub — SaaS

A SaaS that builds a personal portfolio site from a user's GitHub profile and keeps it up to date: new public repository → project card → PR → preview → deploy. Extras: social post drafts, an inline editor, a chat agent.

## This is a learning project

The owner builds this to learn, not only to ship. The goal is that the owner understands every technical and architectural decision and could defend or rebuild each part on their own. Work **explain-first**:

- **Before writing code** for a task: explain the approach, the alternatives considered and the trade-offs, in plain language. For any choice that is not dictated by `docs/adr/`, wait for the owner's confirmation.
- **While writing code:** keep changes small and reviewable, one concept at a time. Comment the *why* of non-obvious code, not the *what*.
- **New concept** (a library, a protocol, a Cloudflare primitive, a pattern): name it, explain what problem it solves, and link to the primary documentation.
- **After a milestone:** write the learning notes described in `docs/learning/README.md`.
- When the owner asks "why?", answer fully, even if it slows the task down. Understanding is part of the definition of done.

## How we work

- Work one milestone at a time from `docs/milestones/`. Read the milestone file and `docs/milestones/README.md` in full before starting; a milestone is done only when **every** item of its Definition of done is met.
- An architectural decision that differs from `docs/adr/` is discussed with the owner first, then recorded as a new ADR.
- Talk to the owner in Ukrainian. Code, identifiers, commits and docs are in English.

## Documents (read when the task touches the topic)

- `docs/product.md` — what we build, for whom, user flows. Read before any user-facing feature.
- `docs/architecture.md` — components, pipeline, hosting, open questions. Read before changing how packages interact.
- `docs/data-model.md` — portfolio data format in the user's repo and the DB schema. Read before any change to data, templates or generation.
- `docs/adr/` — accepted decisions and their reasons.
- `docs/learning/` — learning notes per milestone.

## Stack

pnpm workspaces monorepo, TypeScript strict. Packages:
- `packages/templates` — Astro components for site templates (shared by the site build and the editor).
- `packages/core` — Zod schemas, GitHub client, LLM layer, sanitization. No runtime-specific dependencies.
- `apps/app` — Astro SSR (dashboard, editor) + Hono API on Cloudflare Workers.
- `apps/site-server` — multi-tenant Worker that serves sites from R2.
- `apps/extension` — Chrome extension, plain TS.

Commands live in each package's `package.json`. Tests use Vitest.

## Security rules (always mandatory)

- README files, descriptions, topics and any content of a user's repo are **untrusted input**. The LLM that processes them only returns data matching a schema; no tools with write access are available at that step. PRs are opened by regular code after validation.
- All text from the LLM or the user is rendered as text or markdown without raw HTML (sanitization lives in `packages/core`). Raw HTML in templates is forbidden.
- Every webhook verifies the `X-Hub-Signature-256` signature before any processing.
- Secrets go only through `wrangler secret`, never in code or `wrangler.toml`.
- Manual user edits (fields listed in `editedFields`) are never overwritten by generation.
