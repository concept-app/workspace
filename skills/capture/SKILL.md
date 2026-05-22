---
name: capture
description: Use when the user says "save this", "document this", "put this in Workspace", "share with the team", or otherwise asks to make a chat output durable. Selects the right Concept Workspace file kind, slug, and shape.
---

# Capture

Turn a useful chat output into a durable Concept Workspace file. See
the `concept-workspace` skill for MCP tool mechanics; this skill picks
shape and routing.

## Pick the kind

| User intent                                | `kind`            |
| :----------------------------------------- | :---------------- |
| Reference doc, explanation, FAQ            | `doc`             |
| Step-by-step process or runbook            | `playbook`        |
| Prompt or system-prompt template           | `prompt`          |
| Quick note, scratch, status                | `note`            |
| Product or technical specification         | `spec`            |
| Wrap-up of a run or conversation           | `session_summary` |
| Reusable agent instructions                | use `skill_upsert` |

If the intent is reusable agent behavior, do not capture as a file —
push as a shared skill via the `concept-workspace` skill instead. If
the intent is a transferable mid-task state, delegate to the `handoff`
skill. If the intent is a repeatable team process, delegate to the
`playbook-builder` skill.

## Before writing

1. Call `workspace_file_list` filtered by the chosen `kind`. If a
   relevant file exists, load it with `workspace_file_get` and update
   via `workspace_file_upsert` with `expectedUpdatedAt`. Do not create
   a duplicate.
2. `slug`: kebab-case noun phrase, e.g. `gmail-triage-playbook`.
3. `title`: human-readable noun phrase, not a sentence.

## File shape

```md
# <Title>

## Purpose
<one or two sentences — why this exists>

## Body
<the actual content>

## Source
Captured <YYYY-MM-DD> from <agent or run>.
```

## Never capture

Secrets, API keys, OAuth tokens, cookies, `.env` values, raw Workspace
API keys, upload URLs, verbatim transcripts, or conversational
scaffolding. Distill instead of pasting.

## After

Report the resulting `slug` and `kind` so the user can find it again.
