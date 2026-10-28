# Birdview PSA

Work with [Birdview PSA](https://birdviewpsa.com) (formerly Easy Projects) from Claude, ChatGPT or
Codex. Ask about your projects, tasks, time logs, workload, team availability, PTO, customers,
portfolios, expenses and payments, or create and update tasks, projects, time logs and customers.

## What's inside

- **Birdview connector**: the remote MCP server `https://mcp.go.easyprojects.net/services/mcp`.
  Its `birdview_*` tools read and change Birdview data.
- **Birdview skill** (`skills/birdview`): tells the assistant how to use those tools. It covers
  paging, resolving names to IDs, confirming changes, dry-running bulk edits, linking items to
  Birdview, and Birdview terms (workflows, billable time, financial fields).

The plugin contains no code that runs on your machine and no credentials.

## Install

- **claude.ai and Cowork**: **Customize > Plugins > Add > Add marketplace**, enter
  `LogicSoftware/birdview-ai-plugins`, then install **Birdview PSA**.
- **Claude Code**: `/plugin install birdview --marketplace LogicSoftware/birdview-ai-plugins`
- **Codex and the ChatGPT desktop app**:
  `codex plugin marketplace add LogicSoftware/birdview-ai-plugins`, then
  `codex plugin add birdview@birdview-psa`

## Connect

When you connect, Birdview asks for your account name (the subdomain you use to open Birdview,
e.g. `acme` for `acme.go.easyprojects.net`). You then sign in with your own Birdview user through
OAuth. The assistant acts as that user: it sees and changes only what the user's Birdview
permissions allow.

- **Claude**: **Customize > Plugins > Birdview PSA**, **Connectors** tab, **Connect**.
- **ChatGPT or Codex**: sign in to the Birdview connector when the plugin asks, or run
  `codex mcp login birdview`.

## Data the plugin sends

- Tool calls go only to the Birdview MCP server above, over HTTPS, with the OAuth token of the
  signed-in user. Each call carries the arguments the assistant chose from your request, such as
  search text, IDs, dates, hours or the fields of a task to create or update.
- The MCP server passes each call to your Birdview account and returns the result to the
  assistant. Results become part of your conversation in Claude, ChatGPT or Codex.
- The plugin sends nothing to any other service.
- The assistant changes Birdview data only through these tools. The skill tells it to confirm each
  change with you first, and to preview bulk changes before applying them.

## Privacy

Birdview data that the connector reads or writes is processed under the
[Birdview PSA privacy policy](https://birdviewpsa.com/company/privacy/). What happens to the
conversation, including tool results, is governed by the privacy terms of the assistant you use
(Anthropic for Claude, OpenAI for ChatGPT and Codex).

To stop access, disconnect the Birdview connector or uninstall the plugin in your assistant.

## License

MIT
