# ADR 0001: Host sites on Cloudflare (option B)

**Status:** accepted, 2026-11-11

## Context
Two options were considered: (A) the site lives in the user's repo + GitHub Pages; (B) the user's repo is data storage and we host.

## Decision
B. A multi-tenant Worker serves built sites from R2 by hostname; custom domains through Cloudflare for SaaS; previews are separate R2 prefixes. Data and images are stored in the user's repo.

## Reasons
- GitHub Pages has no preview deploys for PRs, a key step of the flow.
- Fewer onboarding steps (no enabling Pages, no DNS setup for a subdomain).
- Control over builds, caching, domains; room for paid features.
- Low cost: Workers Paid ($5/month) with included requests, R2 with a free tier and no egress fees, 100 custom hostnames free and about $0.10/month each after that (prices as of 2026-11; check current ones).

## Consequences
- A build runner is needed (see `architecture.md`, open questions).
- No per-user Pages project because of account limits.
- Export to GitHub Pages is a possible later feature; the data is already in the repo, so it is cheap.
