---
name: use-mcp-hub
description: Connect to the aiep MCP hub and create or call hosted MCP servers. Use when the user mentions mcp-hub, aiep MCP, aidcec MCP, hub_whoami, hub_server_create, or mcp-hub.aidcec-ai-agent.com.
---

# Use the MCP hub

Management endpoint: `https://mcp-hub.aidcec-ai-agent.com/mcp`

Streamable HTTP, stateless, JSON responses. Header: `Authorization: Bearer` plus the TokenProxy key (`TOKENPROXY_API_KEY` or `~/.aidcec/config.json`). Never print the key. Accept: `application/json, text/event-stream`.

Built-in tools on that URL include `apa_audit_citations`, `apa_format_reference`, `logs_export`, and the `kb_*` knowledge-base tools. A key's own servers are separate.

## Group knowledge base

`kb_*` on `POST /mcp` forwards the caller's bearer key to `https://db.aidcec-ai-agent.com`. Use the group project key. A personal key is rejected. The same key still has its model list, cost cap, quota, and chat log.

- Build: `kb_me`, `kb_crawl_preflight`, `kb_crawl_add`, `kb_resync`, `kb_job_status`, `kb_job_cancel`, `kb_upload` (base64, 3 MB). Larger files: `aiep kb upload`.
- Manage: `kb_sources_list`, `kb_source_get`, `kb_source_update`, `kb_source_delete`, `kb_source_restore`, `kb_parsed_get`, `kb_chunks_list`, `kb_chunk_update`, `kb_chunk_delete`, `kb_text_add`, `kb_embed`.
- Use: `kb_search`, `kb_ask` (`stream` is forced off; the result includes cited chunk ids).

Poll `kb_job_status` after a crawl or upload. Evaluation can call `logs_export` on `/mcp` (`days` 1–30, `limit` at most 200, pass `next` back as `before`, optional `user` to fetch one session id; chats before 24 Oct 2026 have no `user`, so match those by `requestId`), or `GET /v1/account/logs` directly, for that group key.

## A server this key created

Endpoint: `https://mcp-hub.aidcec-ai-agent.com/mcp/u/<srv_id>` with the same bearer key.

Management tools on `/mcp`: `hub_whoami`, `hub_server_create`, `hub_server_list`, `hub_server_get`, `hub_server_update`, `hub_server_delete`, `hub_tool_put`, `hub_tool_delete`, `hub_table_counts`, `hub_table_clear`, `hub_secret_set`, `hub_secret_delete`, `hub_secret_list`, `hub_server_export`.

- Each key owns at most 20 `mcp` servers and 20 `cli` bundles, 20 tools each.
- `kind` is `mcp` or `cli`. `visibility` is `public` or `private`.
- Only the owner edits tools and secrets. A teacher or admin may delete any server. A private server is usable only by its owner.
- An `http` tool calls an `https` URL. A `database` tool exposes `<name>_query`, `<name>_get`, and `<name>_stats` to anyone who can use the server, and `<name>_insert`, `<name>_bulk_insert` (up to 100 rows), `<name>_update`, `<name>_delete` to the owner only. It is not SQL.
- `<name>_query` takes `where` as column to `{operator: value}` (`eq`, `ne`, `lt`, `lte`, `gt`, `gte`, `contains`, `starts_with`), `orderBy`, `order`, and `limit` up to 100. Pass the reply's `next` back as `cursor` until it is null; an empty page with a `next` still has more to read. `<name>_stats` takes `where`, `groupBy`, and `metrics` such as `{"price": ["avg", "max"]}`.
- To load a CSV or Excel file, the owner runs `aiep hub db import <srv_id> <tool> <file> --dry-run`, then again without `--dry-run`. Do not loop `<name>_insert` one row at a time.
- A person can do the same in the browser at `https://mcp-hub.aidcec-ai-agent.com/db/?id=<srv_id>`: browse, filter, stats, upload Excel or CSV, export CSV, clear a table. Point students there instead of writing a UI.
- `hub_table_counts {serverId}` returns rows per table. `hub_table_clear {serverId, name}` deletes every row and keeps the columns (owner only). While a table has rows, `hub_tool_put` refuses to remove or retype a column or to add a required one; clear first, or add the column as optional.

Do not call `hub_secret_set` from chat. Tell the user to run `aiep hub secret set <id> <name>`, or to put `{"env":"VENDOR_API_KEY"}` in `server.json` and run `aiep hub apply`.

To connect Cursor or VS Code, run `aiep mcp config --client cursor --server <srv_id>` or `--client vscode` and let the user paste the snippet. The command prints a placeholder, not the key, unless they pass `--inline-key`.

## From a published class page

A student page on `project-*.aidcec-ai-agent.com` must not hold a key or call this hub directly. It calls the same-site route `POST /mcp/v1` (built-in tools) or `POST /mcp/v1/u/<srv_id>` (the group's own server). The page host adds the group key and forwards the JSON-RPC body here. These routes work only on the published site, not in a file opened on the student's computer.

- `/mcp/v1` allows `apa_audit_citations`, `apa_format_reference`, `kb_me`, `kb_search`, `kb_ask`, and every `dgh_*` tool. `/mcp/v1/u/<srv_id>` allows every tool except names ending in `_insert`, `_update` or `_delete`.
- `tools/list` is trimmed to those tools. A refused call returns JSON-RPC error `-32601` `tool_not_allowed`; in a batch, only that item fails.
- Per site: 30 calls a minute, 64 KB request, 10 messages per batch, 60 s per call, 1 MB response. On a public site, any visitor's `kb_ask` spends the group's monthly budget.
- Full guide: `https://api.aidcec-ai-agent.com/guides/vibe-coding/SKILL.md?download=1` (section "Class MCP tools — `/mcp/v1`").
