# Birdview AI Plugins

Connect AI assistants to [Birdview PSA](https://birdviewpsa.com) through the Birdview MCP server.
One folder per AI tool:

| Folder | Tool | Status |
|--------|------|--------|
| [`plugins/birdview`](plugins/birdview) | Claude (plugin) | Available |
| `chatgpt/` | ChatGPT | Planned |
| `copilot/` | GitHub / Microsoft Copilot | Planned |

## MCP server

```
https://mcp.go.easyprojects.net/services/mcp
```

One URL for every Birdview account. When you connect, Birdview asks for your account name (the
subdomain you use to open Birdview, e.g. `acme`) and you sign in with your own Birdview account
through OAuth. Claude can only see and change what that user is allowed to in Birdview. The plugin
itself contains no credentials.

## Claude (claude.ai and Cowork)

1. **Install the plugin.** Open **Customize > Plugins > Add > Add marketplace**, enter
   `LogicSoftware/birdview-ai-plugins`, then install **Birdview PSA**.
2. **Connect Birdview.** On the plugin's **Connectors** tab, select **Connect**, enter your account
   name and sign in.

The plugin bundles the Birdview connector and a skill that tells Claude how to use it: paging,
confirming changes, dry-running bulk edits and reporting unsupported operations.

## What the connector can do

Read and search projects, tasks, time logs, users, teams, customers, portfolios, PTO, custom fields,
expenses and payments. Create and update tasks, projects, time logs, customers and custom fields,
including bulk task updates with dry-run and confirmation.
