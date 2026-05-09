# Architecture Rules

This repository uses target-based ownership. AI must not treat the whole codebase as one blended surface.

## Targets

### Static Study App

Paths:

- `index.html`

Owns:

- Italian study UI
- lesson content and grammar reference
- quiz and practice behavior
- client-side app state
- static styling and responsive layout

Does not own:

- AI governance policy
- generated governance documentation
- repository publishing workflow

Allowed dependencies:

- none specified

### AI Governance

Paths:

- `.ai-governance-harness/`
- `AGENTS.md`
- `docs/ai/`

Owns:

- repo-specific AI execution policy
- implementation/review/checkpoint contracts
- ownership map and verification rules
- generated AI governance documentation

Does not own:

- Italian study UI behavior
- lesson content
- quiz scoring behavior

Allowed dependencies:

- none specified

## Shared Code Rule

Shared code must be earned.

Before moving code into a shared area, explain:

1. which targets need it
2. why the current owner is no longer the right home
3. why the shared location is stable enough to deserve shared ownership

