# M6: Post drafts

## Scope
- `core/llm`: `generatePost(input) → PostDraft` for the "new project" and "new release" events (release notes + commits between tags).
- The pipeline creates a draft in `post_drafts` for these events; regular pushes generate no post.
- Dashboard: list of drafts, edit, copy, "published", "dismiss", "regenerate".
- Length variants per network: long (LinkedIn), short (X).

## Definition of done
- [ ] A new project and a new release each create one draft; a push without a release creates none; test.
- [ ] The post contains a link to the project on the portfolio site.
- [ ] The short variant fits X's character limit.
- [ ] The style matches `product.md`; examples for 5 projects are saved as fixtures.
- [ ] Post generations count toward `llm_usage` limits.
