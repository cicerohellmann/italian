# AI Answer Contract

## Mutual-Exclusion Rule

Exactly one of `Implementation`, `Review`, or `Checkpoint` may be active.

- `Implementation` may edit files but must not silently include commit or push work.
- `Review` must not edit files unless the task explicitly switches paths.
- `Checkpoint` may stage, commit, or push existing work but must not add fresh implementation.

## Implementation Path

Before editing, declare goal, validated requirement, files, ownership, boundaries, verification plan, and tracker impact.

After editing, summarize behavior, architecture compliance, files changed, verification gathered, cleanup, and remaining review concerns.

## Review Path

Use findings-first output with scope reviewed, findings, boundary verdict, verification gaps, and residual risk.

## Checkpoint Path

Declare scope being checkpointed, current verification status, tracker/doc sync status, git action, and intentionally excluded dirty files.

