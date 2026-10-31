---
name: use-harness-lab
description: >-
  Find and call Harness Lab actions from the aiep MCP hub catalog (domain
  harness) or the aiep CLI. Use when the user mentions Harness Lab, harness
  runs, sessions, spaces, studio proposals, rooms, or arena matches.
  Human-only actions such as publish and approve stay in the Harness Lab web UI.
---

# Use Harness Lab through the hub catalog

Harness Lab actions are not on the default 12-tool MCP list. Reach them through the catalog, or with `aiep harness`.

## Connect

Remote MCP: `https://mcp-hub.aidcec-ai-agent.com/mcp` (Streamable HTTP), header `Authorization: Bearer <TokenProxy key>`.

Or CLI: `aiep` with `--key`, `TOKENPROXY_API_KEY` / `AIDCEC_KEY`, or `aiep login`. Never paste a key into chat.

## Catalog (MCP)

1. `catalog_search` with `query` such as `harness run` and `domain: "harness"`.
2. `catalog_describe` on one `harness.<noun>.<verb>` name for its schema and the exact `aiep harness …` command.
3. `catalog_call` that name with arguments.

The catalog only lists actions your key may use. Harness Lab checks the key again on every call, so a 403 or 404 means no access, not a broken tool.

Names: `harness.project.list|get|parse`, `harness.run.start`, `harness.session.list|show|export`, `harness.exp.run|compare`, `harness.module.test`, `harness.space.list|files|pull|push`, `harness.studio.propose|test|status`, `harness.room.list|messages|post|tasks`, `harness.arena.games|match_create|match_get|replay|leaderboard`.

Space zips are larger than the hub can return. Use `aiep harness space pull <id> --out <file>` instead of `catalog_call harness.space.pull`.

Per-space MCP (`/mcp/space/<id>`) is a separate opt-in connection with its own small tool set.

## Human-only

Publishing a harness version, approving or rejecting a studio proposal, editing grants or live budgets, and minting, rotating or revoking tokens are not callable. `catalog_call` returns `human_only` and points at the Harness Lab web UI (`https://harness.aidcec-ai-agent.com`). Send the user there; there are no `publish` or `approve` CLI verbs.

## CLI

```sh
aiep harness list --json
aiep harness get <id> --json
aiep harness run <replica-id> --follow --json
aiep harness space pull <space-id> --out space.zip
aiep tools call harness.run.start --json "{\"id\":\"…\"}"
aiep schema
```

`--json` goes to stdout; diagnostics go to stderr. Non-TTY stdin never prompts.

## Key

Never log or echo the TokenProxy key.
