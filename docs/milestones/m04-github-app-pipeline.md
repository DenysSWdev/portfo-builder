# M4: Sign-in, GitHub App and automatic pipeline

## Prerequisite
Decisions on `architecture.md` open questions 2–4 → ADRs.

## Scope
- `apps/app`: GitHub sign-in, sessions, a minimal dashboard (site, projects, jobs, `autoMerge` setting).
- GitHub App: installation, storing the installation in D1, obtaining an installation token.
- Webhook handler (Hono) with signature verification; event detection with the chosen mechanism (webhooks and/or Cron).
- Queue: one job per event; pipeline steps 1–8 from `architecture.md`; state in `jobs`.
- PR to the data repo, a comment with the preview link, auto-merge per setting, a production build after merge.
- Onboarding from `product.md` (flow 1).

## Definition of done
- [ ] A new user completes onboarding and gets a published site.
- [ ] A repo becomes public with the `portfolio` topic → a PR with a card and a preview, with no manual steps.
- [ ] A README change → a PR with an updated card; fields in `editedFields` are unchanged.
- [ ] A webhook with an invalid signature is rejected before any processing; test.
- [ ] Redelivery of the same event does not create a duplicate PR; test.
- [ ] A generation or build failure is visible in the dashboard with the reason; the job can be retried.
- [ ] `autoMerge: false` → the PR waits; `true` and all checks green → merge and prod.
- [ ] GitHub App permissions match the ADR and are no broader.
