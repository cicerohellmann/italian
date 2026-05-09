# Project Map

This file names the real ownership boundaries for this repo.

## Product Targets

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

## Known Ambiguities

- `app`: Usually means the static study UI in index.html; confirm before changing governance docs or publishing workflow.
- `website`: Could mean the existing guide in index.html or a new standalone page; confirm whether to edit existing content or create a separate file.
- `exercise`: Could mean quiz data, generated practice behavior, mastery scoring, or static lesson examples; clarify the intended exercise type before broad implementation.
- `commit and push`: This is Checkpoint path work only; do not add fresh implementation while checkpointing.
