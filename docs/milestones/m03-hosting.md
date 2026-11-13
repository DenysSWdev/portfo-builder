# M3: Hosting on Cloudflare

## Prerequisite
A decision on the build runner (`architecture.md`, open question 1) → a new ADR.

## Scope
- `apps/site-server`: a Worker that finds the site by `Host` (D1 + KV cache) and serves files from R2 using the layout in `architecture.md`; a 404 page; caching with the Cache API.
- Build runner: builds the site from a data folder/branch → uploads to `sites/<userId>/prod/` or `preview/<n>/`.
- Preview addresses `pr-<n>--username.preview.<domain>`.
- Cache invalidation after a deploy.

## Definition of done
- [ ] Two test users on different subdomains each get their own site.
- [ ] A preview is reachable at its own address and does not affect prod.
- [ ] After a new deploy, prod shows the new version without a manual cache purge.
- [ ] An unknown hostname → 404, with no leak of other users' data; test.
- [ ] A path with `..` or outside the user's prefix does not serve other users' files; test.
- [ ] A new ADR for the build runner is added to `docs/adr/`.
