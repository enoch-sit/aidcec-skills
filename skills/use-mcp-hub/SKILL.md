---
name: use-mcp-hub
description: Connect to the aidcec MCP hub and create or call hosted MCP servers. Use when the user mentions mcp-hub, aidcec MCP, hub_whoami, hub_server_create, or mcp-hub.aidcec-ai-agent.com.
---

# Use the MCP hub

Management endpoint: `https://mcp-hub.aidcec-ai-agent.com/mcp`

Streamable HTTP, stateless, JSON responses. Header: `Authorization: Bearer` plus the TokenProxy key (`TOKENPROXY_API_KEY` or `~/.aidcec/config.json`). Never print the key. Accept: `application/json, text/event-stream`.

Built-in tools on that URL include `apa_audit_citations` and `apa_format_reference`. A key's own servers are separate.

## A server this key created

Endpoint: `https://mcp-hub.aidcec-ai-agent.com/mcp/u/<srv_id>` with the same bearer key.

Management tools on `/mcp`: `hub_whoami`, `hub_server_create`, `hub_server_list`, `hub_server_get`, `hub_server_update`, `hub_server_delete`, `hub_tool_put`, `hub_tool_delete`, `hub_secret_set`, `hub_secret_delete`, `hub_secret_list`, `hub_server_export`.

- Each key owns at most 20 `mcp` servers and 20 `cli` bundles, 20 tools each.
- `kind` is `mcp` or `cli`. `visibility` is `public` or `private`.
- Only the owner edits tools and secrets. A teacher or admin may delete any server. A private server is usable only by its owner.
- An `http` tool calls an `https` URL. A `database` tool exposes `<name>_query` and `<name>_get` to anyone who can use the server, and `<name>_insert`, `<name>_update`, `<name>_delete` to the owner only.

Do not call `hub_secret_set` from chat. Tell the user to run `aidcec hub secret set <id> <name>`, or to put `{"env":"VENDOR_API_KEY"}` in `server.json` and run `aidcec hub apply`.

To connect Cursor or VS Code, run `aidcec mcp config --client cursor --server <srv_id>` or `--client vscode` and let the user paste the snippet. The command prints a placeholder, not the key, unless they pass `--inline-key`.
