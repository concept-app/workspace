---
name: handoff
description: Use when the user says "write a handoff", "another agent/teammate needs to continue this", "summarize where we are", or otherwise asks to make the current work transferable. Produces a continuation-ready Concept Workspace file using a fixed handoff template.
---

# Handoff

Make the current work transferable to a fresh agent or teammate who has
no access to this chat. See the `concept-workspace` skill for MCP tool
mechanics; this skill defines the handoff contract.

## When to use

- Switching agents (Claude Code -> Codex, Claude Code -> human teammate).
- Ending a long session that will resume later.
- Pausing mid-task ("come back to this Monday").

For a quick recap with no continuation, use `capture` to save a `doc`
instead. Reserve `handoff` for transferable work-in-progress.

## Storage

- `kind`: `doc`
- `slug`: `<task-or-feature>-handoff-<YYYYMMDD>`
- If a prior handoff for the same task exists, update it via
  `workspace_file_upsert` with `expectedUpdatedAt`. Do not create
  sibling handoffs.

## Required template

```md
# <Task Name> - Handoff <YYYY-MM-DD>

## Goal
<single sentence - what success looks like>

## Current State
<done, in flight, blocked>

## Decisions Made
<key choices with one-line rationale each>

## Files And Artifacts
<repo paths, Workspace slugs, PR or branch links>

## Commands Run / Validation
<tests, lints, builds, deploys executed - and their result>

## Blockers / Open Questions
<anything the next agent needs unstuck or answered>

## Suggested Next Steps
<ordered, concrete, ready to execute>

## Notes For Future Agents
<gotchas, dead ends already explored, do-not-retry>
```

## Quality bar

- The handoff stands alone. The reader has no access to current chat
  history.
- Reference repo paths and Workspace slugs, not "the file we just
  edited."
- State commands and their result, not "I ran the tests."
- If a section is empty, write "None" - never delete the heading.
- No secrets, tokens, or raw API keys in the handoff body.

## After

Report the handoff's `slug` and the receiving agent or teammate so
the user can pass the slug along.
