# Concept Workspace

Public plugin and connector package for Concept Workspace.

This repository contains only the files needed for agent clients and platform
reviewers to connect to the hosted Concept Workspace MCP server.

## Endpoint

```text
https://workspace-api.concept.dev/mcp
```

## Included Files

```text
.mcp.json
.codex-plugin/plugin.json
.claude-plugin/plugin.json
.github/workflows/ci.yml
LICENSE
SECURITY.md
```

## Product Metadata

```text
Product name: Concept Workspace
Company: Concept
Support: support@concept.dev
Homepage: https://workspace.concept.dev
Docs: https://docs.concept.dev/docs
Privacy: https://www.concept.dev/legal/privacy-policy
Terms: https://www.concept.dev/legal/terms-of-service
Category: Productivity
Brand color: #111827
License: Proprietary
```

## Codex

Install the plugin from this repo after it is public or available to your
authenticated GitHub user:

```bash
codex plugins install https://github.com/concept-app/workspace.git
codex mcp login concept
```

Or add the MCP endpoint directly:

```bash
codex mcp add concept --url https://workspace-api.concept.dev/mcp
codex mcp login concept
```

## Claude Code

Add the MCP endpoint directly:

```bash
claude mcp add --transport http concept https://workspace-api.concept.dev/mcp
```

For Claude Code plugin submission, use a tagged GitHub release from this repo.

## ChatGPT And Claude Connectors

For ChatGPT and Claude connector submission, use:

```text
MCP endpoint: https://workspace-api.concept.dev/mcp
Public repo: https://github.com/concept-app/workspace
```

Reviewer credentials, passwords, and 2FA/TOTP details must be provided only in
the private platform submission forms. Do not commit them to this repository.

## Safety Boundary

This repo must not contain:

- passwords
- reviewer emails or TOTP secrets
- OAuth client secrets
- API keys
- bearer tokens
- cookies
- customer data
- private Workspace backend code
- connector credential code

Concept Workspace brokers connector access on the hosted service. Agents should
never ask users for API keys, OAuth tokens, cookies, or `.env` values.
