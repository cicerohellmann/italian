# Verification Contract

Do not claim completion without the proof that matches the active execution path and the target scope.

## Execution Path Rule

- `Implementation`: gather proof for the actual change
- `Review`: gather evidence strong enough to support findings
- `Checkpoint`: confirm proof already exists before staging, committing, or pushing

## Verification Rules

### `index.html`

Required proof:

- `script_syntax_check`
- `static_panel_reference_check`
- `manual_or_browser_smoke_for_ui_changes`

### `.ai-governance-harness/`

Required proof:

- `harness_validate`
- `harness_render_check`

### `docs/ai/`

Required proof:

- `harness_validate`
- `consistency_check`
