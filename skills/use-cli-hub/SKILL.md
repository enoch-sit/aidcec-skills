---
name: use-cli-hub
description: Install and run the aidcec CLI for APA checks and for creating MCP or CLI tool bundles. Use when the user mentions aidcec, cli-hub, aidcec hub, aidcec run, or cli-hub.aidcec-ai-agent.com.
---

# Use the CLI hub

Install requires Node.js 22.12 or newer:

```powershell
irm https://cli-hub.aidcec-ai-agent.com/install.ps1 | iex
```

```sh
curl -fsSL https://cli-hub.aidcec-ai-agent.com/install.sh | sh
```

The key is read from `--key`, then `TOKENPROXY_API_KEY`, then `AIDCEC_KEY`, then `~/.aidcec/config.json`. Ask the user to run `aidcec login` themselves. Never ask them to paste a key into chat, and never print a key. `--verbose` shows only the first 15 characters.

## Commands

```sh
aidcec apa audit paper.docx --json          # exit 0 clean, 2 needs attention, 1 error
aidcec apa format "Author, A. A. (2020). Title." --json
aidcec hub whoami
aidcec hub init -o server.json
aidcec hub apply -f server.json
aidcec hub list --mine
aidcec run <slug-or-srv_id> <tool> --json '{"q":"…"}'
aidcec mcp config --client cursor --server srv_…
```

`hub apply` creates or updates the server and its tools. In `server.json`, a third-party key is `{"env":"VENDOR_API_KEY"}`, not a literal string. Set that variable in the shell before apply. Otherwise the user runs `aidcec hub secret set <id> <name>` and types the value at the hidden prompt.

`aidcec run` calls a tool on the owner's server or a public server. `mcp config` prints a client snippet. It does not write Cursor or VS Code settings.

Docs: https://cli-hub.aidcec-ai-agent.com/
