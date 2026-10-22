---
name: aidcec-start
description: >-
  Set up a fresh coding agent for aidcec and then use TokenProxy, the MCP hub,
  and the aidcec CLI. Use when the user mentions a fresh start, student setup,
  onboarding, installing aidcec, TokenProxy, tp-live, mcp-hub, cli-hub,
  aidcec hub, api.aidcec-ai-agent.com, mcp-hub, or cli-hub.
---

# Start aidcec

Do this once on a machine that does not yet have aidcec. Then use the product sections below. Never ask the user to paste a TokenProxy key into chat, and never print a key.

## 1. Install the product skills

Requires GitHub CLI (`gh`). If `gh` is missing, stop and tell the user to install it from https://cli.github.com/.

Use `--agent cursor` in Cursor. Use `--agent github-copilot` in VS Code. `--scope user` makes the skills available in every project.

```sh
gh skill install enoch-sit/aidcec-skills use-tokenproxy --agent cursor --scope user
gh skill install enoch-sit/aidcec-skills use-mcp-hub --agent cursor --scope user
gh skill install enoch-sit/aidcec-skills use-cli-hub --agent cursor --scope user
```

After they install, follow `use-tokenproxy`, `use-mcp-hub`, or `use-cli-hub` for that product. This file remains enough to operate if those installs fail.

## 2. Install the CLI

Requires Node.js 22.12 or newer. If `node -v` is older or missing, stop and tell the user to install it from https://nodejs.org/.

Windows (PowerShell):

```powershell
irm https://cli-hub.aidcec-ai-agent.com/install.ps1 | iex
```

macOS or Linux:

```sh
curl -fsSL https://cli-hub.aidcec-ai-agent.com/install.sh | sh
```

Confirm with `aidcec --version`. Then tell the user to run `aidcec login` themselves. That checks the key and writes `~/.aidcec/config.json`.

Key lookup order for every command: `--key`, then `TOKENPROXY_API_KEY`, then `AIDCEC_KEY`, then `~/.aidcec/config.json`. `--verbose` shows only the first 15 characters.

## TokenProxy

Host: `https://api.aidcec-ai-agent.com`. Header: `Authorization: Bearer` plus the key, and `Content-Type: application/json`.

Before a long job, `GET /v1/account` returns `allowedModels`, `defaultModel`, `costCapUsd`, `costCapPeriod`, and `spentMicro` (micro-USD). Stop if the remaining cap cannot cover the job. `GET /v1/models` lists models this key may call.

`model` must be one of the key's allowed models. Prefer `defaultModel`. Use `tokenproxy/fast` for cheap text and `tokenproxy/vision` when the input includes an image.

```http
POST /v1/chat/completions
{ "model": "tokenproxy/vision", "messages": [{ "role": "user", "content": "…" }], "max_tokens": 1024, "stream": false }
```

```http
POST /v1/embeddings
{ "model": "tokenproxy/embed", "input": "…" }
```

Same bearer key: `POST /v1/images/generations` (`tokenproxy/image`), `POST /v1/videos` then `GET /v1/videos/:id` (`tokenproxy/video`), `POST /v1/decide` (`tokenproxy/decide`). A missing, unknown, or disabled key is 401. A model the key cannot use is 400.

Docs: https://api.aidcec-ai-agent.com/guides

## MCP hub

Management endpoint: `https://mcp-hub.aidcec-ai-agent.com/mcp`

Streamable HTTP, stateless, JSON responses. Same bearer key. Accept: `application/json, text/event-stream`. Built-in tools there include `apa_audit_citations` and `apa_format_reference`.

A server this key created: `https://mcp-hub.aidcec-ai-agent.com/mcp/u/<srv_id>`.

Management tools on `/mcp`: `hub_whoami`, `hub_server_create`, `hub_server_list`, `hub_server_get`, `hub_server_update`, `hub_server_delete`, `hub_tool_put`, `hub_tool_delete`, `hub_secret_set`, `hub_secret_delete`, `hub_secret_list`, `hub_server_export`.

- Each key owns at most 20 `mcp` servers and 20 `cli` bundles, 20 tools each.
- `kind` is `mcp` or `cli`. `visibility` is `public` or `private`.
- Only the owner edits tools and secrets. A teacher or admin may delete any server. A private server is usable only by its owner.
- An `http` tool calls an `https` URL. A `database` tool exposes `<name>_query` and `<name>_get` to anyone who can use the server, and `<name>_insert`, `<name>_update`, `<name>_delete` to the owner only.

Do not call `hub_secret_set` from chat. Tell the user to run `aidcec hub secret set <id> <name>`, or to put `{"env":"VENDOR_API_KEY"}` in `server.json` and run `aidcec hub apply`.

To connect Cursor or VS Code, run `aidcec mcp config --client cursor --server <srv_id>` or `--client vscode` and let the user paste the snippet. The command prints a placeholder, not the key, unless they pass `--inline-key`.

## CLI

```sh
aidcec apa audit paper.docx --json
aidcec apa format "Author, A. A. (2020). Title." --json
aidcec hub whoami
aidcec hub init -o server.json
aidcec hub apply -f server.json
aidcec hub list --mine
aidcec run <slug-or-srv_id> <tool> --json '{"q":"…"}'
aidcec mcp config --client cursor --server srv_…
```

`apa audit` exits 0 when clean, 2 when something needs attention, and 1 on error. In `server.json`, a third-party key is `{"env":"VENDOR_API_KEY"}`, not a literal string. Set that variable in the shell before `hub apply`.

Docs: https://cli-hub.aidcec-ai-agent.com/
