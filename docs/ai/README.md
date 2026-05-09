# AI Governance Docs

This folder is the repo-wide AI quality system for `italian`.

## Reading Order

1. `AGENTS.md`
2. `docs/ai/README.md`
3. `docs/ai/ARCHITECTURE.md`
4. `docs/ai/PROJECT_MAP.md`
5. `docs/ai/VERIFICATION_CONTRACT.md`
6. `docs/ai/AI_ANSWER_CONTRACT.md`
7. `docs/ai/AI_CODE_REVIEW.md`

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

Exactly one path may be active.

- Do not implement during `Review`.
- Do not commit or push during `Implementation` unless the task explicitly switches to `Checkpoint`.
- Do not add fresh implementation during `Checkpoint`.

## Tracked Workflows

- no tracker-backed workflows declared
