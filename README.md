# aidcec skills

Give a fresh agent this one command. `aidcec-start` installs the CLI and the three product skills, and it carries the operating guide for TokenProxy, the MCP hub, and the aidcec CLI.

```sh
gh skill install enoch-sit/aidcec-skills aidcec-start --agent cursor --scope user
```

For VS Code, use `--agent github-copilot`. Omit `--scope user` to install into the current project's `.agents/skills`.

The product skills can also be installed on their own:

```sh
gh skill install enoch-sit/aidcec-skills use-tokenproxy --agent cursor --scope user
gh skill install enoch-sit/aidcec-skills use-mcp-hub --agent cursor --scope user
gh skill install enoch-sit/aidcec-skills use-cli-hub --agent cursor --scope user
```
