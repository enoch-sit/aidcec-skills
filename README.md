# aidcec skills

Skills for the TokenProxy API, the MCP hub, and the aidcec CLI. Each skill is a `skills/<name>/SKILL.md` folder so GitHub CLI can install it.

```sh
gh skill install enoch-sit/aidcec-skills use-tokenproxy --agent cursor --scope user
gh skill install enoch-sit/aidcec-skills use-mcp-hub --agent cursor --scope user
gh skill install enoch-sit/aidcec-skills use-cli-hub --agent cursor --scope user
```

For VS Code, use `--agent github-copilot`. Omit `--scope user` to install into the current project's `.agents/skills`.
