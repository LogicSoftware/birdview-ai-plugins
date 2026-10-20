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

## Claude (claude.ai and Cowork)

1. **Connect Birdview.** In Claude, open **Settings > Connectors > Add custom connector**, enter your
   account URL (above) and sign in with your Birdview account.
2. **Install the plugin.** Open **Customize > Plugins > Add > Add marketplace**, enter
   `LogicSoftware/birdview-ai-plugins`, then install **Birdview PSA**. The plugin adds a skill that
   tells Claude how to use the connector: paging, confirming changes, dry-running bulk edits and
   reporting unsupported operations.

The connector URL depends on your account, so the plugin doesn't include it. Claude asks you to
connect it if it's missing.

## What the connector can do

Read and search projects, tasks, time logs, users, teams, customers, portfolios, PTO, custom fields,
expenses and payments. Create and update tasks, projects, time logs, customers and custom fields,
including bulk task updates with dry-run and confirmation.
