---
name: use-cli-hub
description: Install and run the aiep CLI for APA checks and for creating MCP or CLI tool bundles. Use when the user mentions aiep, aidcec, cli-hub, aiep hub, aiep run, or cli-hub.aidcec-ai-agent.com.
---

# Use the CLI hub

Install requires Node.js 22.12 or newer:

```powershell
irm https://cli-hub.aidcec-ai-agent.com/install.ps1 | iex
```

```sh
curl -fsSL https://cli-hub.aidcec-ai-agent.com/install.sh | sh
```

The key is read from `--key`, then `TOKENPROXY_API_KEY`, then `AIDCEC_KEY`, then `~/.aidcec/config.json`. Ask the user to run `aiep login` themselves. Never ask them to paste a key into chat, and never print a key. `--verbose` shows only the first 15 characters.

## Commands

```sh
aiep apa audit paper.docx --json          # exit 0 clean, 2 needs attention, 1 error
aiep apa format "Author, A. A. (2020). Title." --json
aiep hub whoami
aiep kb upload paper.pdf --paper
aiep kb crawl https://example.com/news
aiep kb ls
aiep kb search "opening hours"
aiep kb ask "When does the desk close?"
aiep hub init -o server.json
aiep hub apply -f server.json
aiep hub list --mine
aiep run <slug-or-srv_id> <tool> --json '{"q":"…"}'
aiep mcp config --client cursor --server srv_…
```

`hub apply` creates or updates the server and its tools. In `server.json`, a third-party key is `{"env":"VENDOR_API_KEY"}`, not a literal string. Set that variable in the shell before apply. Otherwise the user runs `aiep hub secret set <id> <name>` and types the value at the hidden prompt.

`aiep hub db import <srv_id> <tool> <file.csv|.xlsx>` loads a spreadsheet into a database tool, 100 rows a call. Headers must match column names, or use `--map column=Header`. Run it with `--dry-run` first. On failure it prints the `--from-row` to resume at.

`aiep run` calls a tool on the owner's server or a public server. `mcp config` prints a client snippet. It does not write Cursor or VS Code settings.

`aiep kb` needs the group project key. `upload` sends the file straight to the knowledge base. `crawl`, `ls`, `search`, and `ask` call the `kb_*` tools on mcp-hub. A crawl prints a job id; poll it with `aiep tools call kb_job_status --json "{\"id\":\"job_…\"}"`. `ask` does not stream. The chat is logged on that group key.

Docs: https://cli-hub.aidcec-ai-agent.com/
