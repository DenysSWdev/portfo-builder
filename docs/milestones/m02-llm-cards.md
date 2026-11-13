# M2: Project cards via LLM

## Goal
Turn a README into a portfolio card: what it is, what problem it solves, stack, demo.

## Scope
- `packages/core/llm`: interface `generateProjectCard(input) → ProjectCard`, implemented with the Anthropic SDK, models from config, tool use with a JSON schema, prompt caching of the system prompt.
- Input: README, languages, topics, homepage, repo description. The README goes in as data in a delimited block.
- Zod validation, one retry with the validation error, then an error.
- Merge with the existing project file, respecting `editedFields`.
- The `generate` CLI gets an `--llm` flag.
- Style: restrained, concrete, in the language from `portfolio.config.json`; no marketing clichés or emojis.

## Definition of done
- [ ] On 10 real READMEs of different quality (empty, purely technical, good) generation produces valid cards; the examples are saved as fixtures.
- [ ] An empty or nearly empty README produces a correct short card, not invented facts.
- [ ] Test: a README with prompt injection ("ignore previous instructions…") does not change the output format and adds no foreign content.
- [ ] Test: fields in `editedFields` do not change after regeneration.
- [ ] No module outside `core/llm` imports the Anthropic SDK.
- [ ] The API key comes only from env; a missing key gives a clear error.
