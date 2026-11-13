# ADR 0004: Data separate from templates

**Status:** accepted, 2026-11-11

## Decision
The user's repo holds only structured data (JSON + markdown with frontmatter) and assets. Templates live in `packages/templates` and render that data. Agents, the editor and the pipeline change only data, never HTML.

## Reasons
- Pipeline PRs are small and readable (one project file).
- A template can change without regenerating content.
- Data is validated by a schema, which is a check before auto-merge.
- The inline editor maps `data-field` to a data field without parsing HTML.
- Manual edits are protected by the `editedFields` field.
