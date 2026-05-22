---
name: context
description: Use at the start of any non-trivial task, or when the user says "check Workspace first", "what do we know about X", or any prompt where existing team context (specs, playbooks, decisions, prior handoffs) would change the approach. Loads relevant Concept Workspace context before doing work.
---

# Context

Before starting non-trivial work, pull what the team already knows from
Concept Workspace so the session does not start from zero. Use the
Workspace MCP search and file tools; this skill defines search strategy
and stop condition.

## Skip when

- The task is a trivial one-shot edit (rename, format, typo fix).
- The user explicitly said "don't search Workspace" or "fresh start."

## Discovery order

1. `skill_search` with the task's primary noun. `skill_load` the top
   one or two if relevant.
2. `workspace_file_list` filtered by `kind: spec`, then `kind: playbook`,
   then `kind: session_summary`. Stop at the first `kind` that yields
   a relevant result.
3. For the most relevant one to three files, call `workspace_file_get`
   and read them in full.
4. If the task touches a live external system, also call
   `connector_list` so the user knows which connectors are usable.

## How to use what you find

- A matching `spec` → treat as authoritative for product behavior.
- A matching `playbook` → follow its steps unless the user opts out.
- A matching `session_summary` (handoff) → resume from its
  "Suggested Next Steps", do not restart.
- A matching `skill` → load it through the shared skill workflow if it changes how this task should be done.

## Stop condition

Stop searching once any one is true:

- A `spec` or `playbook` covers the task.
- A recent handoff (≤14 days) covers the task.
- Three list calls returned nothing relevant.

## Report

Before doing work, name the Workspace files (`slug` + `kind`) you
loaded and one sentence per file on what each contributed. If nothing
relevant existed, say so explicitly so the user knows context was
checked.
