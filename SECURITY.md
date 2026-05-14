# Security

Report security issues to `hello@concept.dev`.

Please do not open public GitHub issues for suspected vulnerabilities, credential exposure, account access problems, or private customer data.

## Public Repository Boundary

This repository is intended to be safe to make public. It contains only plugin manifests, MCP configuration, and minimal setup/security notes for connecting to the hosted Concept Workspace MCP service.

It must not contain:

- passwords
- TOTP secrets
- OAuth client secrets
- API keys
- bearer tokens
- cookies
- customer data
- private database URLs
- private connector credentials
- reviewer account secrets

Reviewer credentials are provided only through private OpenAI or Anthropic submission forms.

## Hosted Service

The production MCP endpoint is:

```text
https://workspace-api.concept.dev/mcp
```

Concept Workspace brokers connector access server-side. Agents should never request or receive provider credentials directly.
