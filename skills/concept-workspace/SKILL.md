---
name: concept-workspace
description: Reference for the Concept Workspace MCP server — connection model, tool catalog, and the canonical mechanics for shared skills, files, uploads, and brokered connector tools. Other plugin skills (capture, handoff, context, playbook-builder) defer to this skill for tool usage.
---

# Concept Workspace

Concept Workspace is the shared system of record for durable team context.
Use it for:

- Skills the team has agreed on, stored centrally and pulled into each
  agent's local skills directory or loaded on demand via MCP.
- Concept Workspace files (docs, playbooks, prompts, notes, specs, session
  summaries) that survive past a single conversation.
- Brokered connector tools (Gmail, Slack, Notion, Linear, etc.) executed
  with credentials Workspace holds on the user's behalf.

## Connection

The user added Concept Workspace as an MCP server through the
`/connect-agents` page in the Concept Workspace UI and signed in to
Concept Workspace through the OAuth flow in this client. The server URL
follows `<workspace-api-origin>/mcp` — production is
`https://workspace-api.concept.dev/mcp`.

For automation outside an interactive client (scripts, CI, long-running
agents) a user can mint a Concept Workspace API key from the
Concept Workspace UI and pass it as a bearer credential. Production keys are
`wsk_live_<keyId>_<secret>`; non-production keys are
`wsk_test_<keyId>_<secret>`. Raw keys are shown once at creation. Never
write a raw key, bearer token, one-time code, or invite token into a
skill body, workspace file, prompt, log, ticket, or chat transcript.

Never ask the user for connector API keys, OAuth credentials, tokens,
cookies, or `.env` values. Concept Workspace brokers connector access.

## MCP tools

The Concept Workspace MCP server exposes:

- `skill_search` — list shared Concept Workspace skills (metadata only).
  Optional `query`, `limit`.
- `skill_load` — load one skill by `name`. Response includes
  `skill.sourceMarkdown`: the full SKILL.md with frontmatter, ready to
  write to disk or hand to a skill-creator.
- `skill_upsert` — create or update a shared skill. Parameters: `name`,
  `title`, `description`, `bodyMarkdown` (body only, no frontmatter),
  `metadataJson` (optional), `expectedUpdatedAt` (optional optimistic
  lock).
- `skill_delete` — delete a shared skill by `name`.
- `workspace_file_list`, `workspace_file_get`, `workspace_file_upsert`,
  `workspace_file_delete` — same CRUD shape for non-skill workspace
  files. `kind` is one of `doc`, `playbook`, `prompt`, `note`, `spec`,
  `session_summary`, `skill`, `workflow`.
- `workspace_upload_prepare` — create a short-lived upload URL for a
  local text/Markdown/JSON file that should be imported without placing
  the full file body in MCP JSON-RPC arguments.
- `workspace_file_import` — import a completed upload into the canonical
  Concept Workspace file store by `uploadId`.
- `connector_list` — list available connectors and their connection
  status. Accepts `connectedOnly`.
- `connector_tool_search` — discover connector tools surfaced through
  Concept Workspace-brokered credentials. Results include `annotations`,
  `safety`, and a short-lived `executionToken`.
- `connector_tool_execute_read` — run a read-only connector tool returned
  by `connector_tool_search`.
- `connector_tool_execute` — run one of those tools by `executionToken`
  or `toolName` and `arguments`. Mutating and destructive tools require
  an explicit `safetyAcknowledgement`.

## Push a local skill to Workspace

When importing a local skill (Claude Code at
`~/.claude/skills/<name>/SKILL.md`, Codex CLI at
`~/.codex/skills/<name>/SKILL.md`) into Workspace:

1. Read the local SKILL.md.
2. Parse the YAML frontmatter. Confirm `name` matches `<name>` and
   `description` is present.
3. Call `skill_upsert` with:
   - `name`: `<name>`
   - `title`: from frontmatter if present, otherwise a humanized form
   of `name`
   - `description`: from frontmatter
   - `bodyMarkdown`: everything after the frontmatter block — do not
   include the frontmatter itself
4. Do not modify other local skills.

## Pull a Workspace skill into a local agent

When installing a Workspace skill back into a local agent:

1. Call `skill_load({ name: <name> })`.
2. `skill.sourceMarkdown` is the full SKILL.md (frontmatter and body).
   Install via whichever path this runtime supports:
   - Write `sourceMarkdown` verbatim to `~/.claude/skills/<name>/SKILL.md`
   (Claude Code) or `~/.codex/skills/<name>/SKILL.md` (Codex CLI).
   - If a `/skill-creator` skill is available in this session, pass
   `sourceMarkdown` to it.
   - On host-managed runtimes (claude.ai, Claude Cowork, Codex web) the
   local skill store is read-only — skip local install and rely on
   `skill_load` on demand. This is a normal outcome, not a failure.
3. Skip skills that already exist locally with the same `name`. Do not
   overwrite or rename.

## Read and write Concept Workspace files

1. Call `workspace_file_list` (optionally filtered by `kind`) to discover
   relevant shared files.
2. Call `workspace_file_get` with exactly one of `id` or `slug` to load
   a full file.
3. Call `workspace_file_upsert` to write or update. Prefer updating
   existing files over creating near-duplicates.
4. Pass `expectedUpdatedAt` on `workspace_file_upsert` and
   `workspace_file_delete` for optimistic locking against the last
   `updatedAt` you read.

## Upload and import local files

Use the upload flow when importing a local text, Markdown, or JSON file
large enough that copying the body into a tool argument would be awkward.
Do not use it for binary files.

1. Call `workspace_upload_prepare` with `filename`, `contentType`, and
   `sizeBytes`. The maximum upload size is reported as `maxBytes`.
2. Upload the file bytes with the returned short-lived `uploadUrl`.
   This requires a local-capable host, CLI, or browser UI. A remote MCP
   server cannot read a client-local filesystem path by itself.
3. Call `workspace_file_import` with `uploadId`, `kind`, `slug`, and
   `title`. Pass `expectedUpdatedAt` if replacing a file you already
   read and want conflict protection.
4. Treat the upload URL as secret. Do not write it into skills,
   Concept Workspace files, logs, tickets, prompts, or chat transcripts.

## Use Concept Workspace-brokered connectors

1. Call `connector_list` (or `connector_list({ connectedOnly: true })`)
   before using connector tools.
2. If the needed connector is not connected, tell the user which
   connector to connect in Concept Workspace. Do not try to authenticate
   the connector yourself.
3. Use `connector_tool_search` to discover available tools. Prefer
   `connector_tool_execute_read` for read-only actions, and use
   `connector_tool_execute` for mutating actions only after explicit user
   confirmation.
4. Pass only tools, execution tokens, and arguments surfaced by
   Concept Workspace.
5. Concept Workspace executes connector tools through backend-held
   credentials and does not return provider tokens.
