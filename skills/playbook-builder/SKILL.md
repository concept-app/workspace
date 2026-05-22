---
name: playbook-builder
description: Use when the user says "make this repeatable", "turn this into a playbook or runbook", "write the process so the next person can do it", or otherwise asks to convert a one-off workflow into reusable team operating knowledge.
---

# Playbook Builder

Convert a workflow that has been executed once (or a few times) into a
shared, re-executable team playbook. See the `concept-workspace` skill
for MCP tool mechanics; this skill defines the playbook contract.

## Do not use when

- The workflow has been done once and may not recur — `capture` as
  `note` or `session_summary` instead.
- The workflow is fully encoded in code or CI — reference the code,
  do not re-document it.
- The output is reusable agent behavior — push as a shared skill via
  `skill_upsert` instead.

## Storage

- `kind`: `playbook`
- `slug`: `<verb>-<object>-playbook`, e.g.
  `triage-incoming-leads-playbook`.
- Call `workspace_file_list({ kind: "playbook" })` first. Update an
  existing playbook with `expectedUpdatedAt` rather than creating a
  duplicate.

## Required template

```md
# <Verb-Object> Playbook

## When To Run
<concrete triggers: events, schedule, user requests>

## Owners
<role or team responsible, not individuals>

## Inputs
<what the operator or agent must have on hand before starting>

## Required Connectors / Tools
<Concept Workspace connectors, repos, systems touched>

## Steps
1. <imperative, single action — expected observable result>
2. <imperative, single action — expected observable result>
...

## Validation
<how the operator confirms the playbook completed successfully>

## Failure Modes
<known ways this can go wrong and how to recover>

## Change Log
- YYYY-MM-DD: <change>
```

## Quality bar

- Steps are imperative and single-action ("Open Linear issue", not
  "you can open a Linear issue and maybe tag it").
- No step depends on memory from the original session — each step is
  re-executable cold.
- Connector names match `connector_list` exactly, not vendor brand
  language.
- Every step states what observable thing is true after it runs.
- No secrets, tokens, or raw API keys in the playbook body.

## After

Report the playbook's `slug` and link the user to where it lives so
the team can run it next time.
