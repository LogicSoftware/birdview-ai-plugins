# Birdview AI Plugins

Connect AI assistants to [Birdview PSA](https://birdviewpsa.com) through the Birdview MCP server.
One plugin, [`plugins/birdview`](plugins/birdview), serves several AI tools. Each tool reads its own
manifest, and they share the connector URL and the skill:

| Tool | Manifest | MCP config | Marketplace | Status |
|------|----------|------------|-------------|--------|
| Claude | `.claude-plugin/plugin.json` | `.mcp.json` | `.claude-plugin/marketplace.json` | Available |
| ChatGPT / Codex | `plugin.json` | `mcp.json` | `.agents/plugins/marketplace.json` | Testing |
| Microsoft 365 Copilot | `m365/appPackage/manifest.json` | `m365/appPackage/ai-plugin.json` | | Testing |
| GitHub Copilot | | | | Planned |

Edit the skill in `plugins/birdview/skills/` once; Claude, ChatGPT and Codex pick it up. Microsoft 365
Copilot keeps a copy in `m365/appPackage/instruction.txt` (see [`plugins/birdview/m365`](plugins/birdview/m365)).

## MCP server

```
https://mcp.go.easyprojects.net/services/mcp
```

One URL for every Birdview account. When you connect, Birdview asks for your account name (the
subdomain you use to open Birdview, e.g. `acme`) and you sign in with your own Birdview account
through OAuth. The assistant can only see and change what that user is allowed to in Birdview. The plugin
itself contains no credentials.

## Claude (claude.ai and Cowork)

1. **Install the plugin.** Open **Customize > Plugins > Add > Add marketplace**, enter
   `LogicSoftware/birdview-ai-plugins`, then install **Birdview PSA**.
2. **Connect Birdview.** On the plugin's **Connectors** tab, select **Connect**, enter your account
   name and sign in.

The plugin bundles the Birdview connector and a skill that tells Claude how to use it: paging,
confirming changes, dry-running bulk edits and reporting unsupported operations.

## ChatGPT and Codex

Adding this repository as a marketplace works in Codex and the ChatGPT desktop app:

```bash
codex plugin marketplace add LogicSoftware/birdview-ai-plugins
codex plugin add birdview@birdview-psa
```

Then sign in to the Birdview connector when the plugin asks (or run `codex mcp login birdview`).
ChatGPT on the web can't add a GitHub marketplace: there the plugin comes from the ChatGPT plugin
directory, after OpenAI reviews it, or from a workspace admin who shares it with the workspace.

## What the connector can do

Read and search projects, tasks, time logs, users, teams, customers, portfolios, PTO, custom fields,
expenses and payments. Create and update tasks, projects, time logs, customers and custom fields,
including bulk task updates with dry-run and confirmation.
