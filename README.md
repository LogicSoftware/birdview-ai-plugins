# Birdview AI Plugins

Connect AI assistants to [Birdview PSA](https://birdviewpsa.com) through the Birdview MCP server.
One folder per AI tool:

| Folder | Tool | Status |
|--------|------|--------|
| [`claude/birdview`](claude/birdview) | Claude (plugin) | Available |
| `chatgpt/` | ChatGPT | Planned |
| `copilot/` | GitHub / Microsoft Copilot | Planned |

## MCP server

```
https://<your-account>.go.easyprojects.net/services/mcp
```

Each person signs in with their own Birdview account through OAuth. Claude can only see and change
what that user is allowed to in Birdview. The plugin itself contains no credentials.

## Claude

Add the marketplace, then install the `birdview` plugin. You'll be asked for your Birdview account
subdomain (`acme` for `acme.go.easyprojects.net`).

```bash
claude plugin marketplace add LogicSoftware/birdview-ai-plugins
claude plugin install birdview@birdview-psa
```

In claude.ai: **Customize > Plugins > Add > Add marketplace** and enter `LogicSoftware/birdview-ai-plugins`.

> **Limitation:** the connector URL depends on your account subdomain. claude.ai chat and Cowork don't
> prompt for plugin settings, so the connector loads in Claude Code only. In claude.ai chat, add it
> as a custom connector instead: **Settings > Connectors > Add custom connector** with the URL above.

## What the connector can do

Read and search projects, tasks, time logs, users, teams, customers, portfolios, PTO, custom fields,
expenses and payments. Create and update tasks, projects, time logs, customers and custom fields,
including bulk task updates with dry-run and confirmation.
