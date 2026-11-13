# ADR 0002: Stack — Astro + Hono on Cloudflare Workers + plain TS

**Status:** accepted, 2026-11-11

## Decision
- TypeScript strict, pnpm workspaces monorepo.
- **Astro** for site templates (static build) and the app UI (SSR on Workers). One set of components for the site and the editor.
- **Hono** for the API and webhooks on Cloudflare Workers.
- **Plain TS** for the editor's client script and the Chrome extension.
- **Zod** for data schemas; **Vitest** for tests.

## Reasons
- Next.js is too heavy for the task; no framework at all means writing routing, middleware and sessions ourselves.
- Hono is minimal and native to Workers; Astro outputs static HTML with no JS by default and has image optimization.
- Well-known frameworks give agents predictable patterns, and give the owner well-documented concepts to learn.
- All infrastructure lives in one ecosystem, Cloudflare.
