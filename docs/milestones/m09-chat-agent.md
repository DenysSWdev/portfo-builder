# M9: Chat agent

## Scope
- `core/llm`: `chatEdit` on Sonnet; the agent has tools only for reading current data and proposing changes as a field diff.
- The diff is applied by regular code with schema validation → PR → preview (the same path as M5).
- Chat in the dashboard that shows the proposed diff before applying it.
- Changes made on a direct user instruction add the field to `editedFields`.

## Definition of done
- [ ] "Make project X's description shorter" → a diff of only that project's field → a PR with a preview.
- [ ] The user sees the diff and confirms it before a PR is created.
- [ ] The agent has no tools that write to the repo; the PR is created by code after validation; test.
- [ ] A request outside the portfolio data gets a refusal with an explanation.
- [ ] Conversations count toward `llm_usage` limits.
