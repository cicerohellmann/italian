# italian — Agent Guide

A static Italian study site with lesson notes, grammar reference, quiz practice, and future mastery-based exercises. AI governance matters here because most work changes learning content, scoring behavior, or generated study UI directly in a single HTML surface.

## AI Governance Docs

Before substantial AI-driven work, also read:

- `docs/ai/README.md`
- `docs/ai/ARCHITECTURE.md`
- `docs/ai/PROJECT_MAP.md`
- `docs/ai/VERIFICATION_CONTRACT.md`
- `docs/ai/AI_ANSWER_CONTRACT.md`
- `docs/ai/AI_CODE_REVIEW.md`

Use these when they apply:

- `docs/ai/AI_REQUEST_TEMPLATE.md`
- `docs/ai/TRACKER_AND_PROGRESSION_RULES.md`
- `docs/ai/DECISION_RECORD_TEMPLATE.md`

## Execution Paths

### Implementation

Allowed actions:

- `edit_docs`
- `edit_code`
- `edit_tests`
- `edit_assets`

Forbidden actions:

- `commit`
- `push`

### Review

Allowed actions:

- `inspect`
- `report_findings`

Forbidden actions:

- `edit_docs`
- `edit_code`
- `edit_tests`
- `commit`
- `push`

### Checkpoint

Allowed actions:

- `stage`
- `commit`
- `push`

Forbidden actions:

- `new_implementation`

## Mutual-Exclusion Rule

Exactly one of `Implementation`, `Review`, or `Checkpoint` may be active at a time.

- `Implementation` must not silently become commit or push work.
- `Review` must not silently become file-editing work.
- `Checkpoint` must not silently introduce fresh implementation.
- If the task changes path, say so explicitly before continuing.

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

## High-Risk Ambiguities

- `app`: Usually means the static study UI in index.html; confirm before changing governance docs or publishing workflow.
- `website`: Could mean the existing guide in index.html or a new standalone page; confirm whether to edit existing content or create a separate file.
- `exercise`: Could mean quiz data, generated practice behavior, mastery scoring, or static lesson examples; clarify the intended exercise type before broad implementation.
- `commit and push`: This is Checkpoint path work only; do not add fresh implementation while checkpointing.
