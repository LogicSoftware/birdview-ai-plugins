# Birdview PSA for Microsoft 365 Copilot

A Microsoft 365 Copilot declarative agent with the Birdview MCP server as its action. Built with
[Microsoft 365 Agents Toolkit](https://aka.ms/M365AgentsToolkit) (VS Code extension, 6.12+).

## How it signs in

Copilot only supports static OAuth registration (or DCR, which Birdview doesn't have), so it uses the
**system OAuth client `m365Copilot`** in Birdview (`SYS_OAuthClients`, one per environment, redirect URI
`https://teams.microsoft.com/api/platform/v1.0/oAuthRedirect`, scope `user`). Provision registers it in
the Teams developer portal for any Teams app and any Microsoft 365 organization, so customers don't register
anything: each user selects **Sign in**, enters the Birdview account name and signs in.

## Files

- `appPackage/instruction.txt`: the agent's instructions, made from `../skills/birdview/SKILL.md` without
  the Claude-specific parts. Copilot allows 8,000 characters. Update it when the skill changes.
- `appPackage/ai-plugin.json`: the MCP action; the server URL comes from `BIRDVIEW_MCP_ORIGIN`.
- `env/.env.<env>.sample`: settings per environment. `dev` is `mcp.dev.dev.logicsoftware.net`, `prod` is
  `mcp.go.easyprojects.net`.

## Provision

1. Copy `env/.env.dev.sample` to `env/.env.dev`.
2. Create `env/.env.dev.user` with `SECRET_BIRDVIEW_OAUTH_CLIENT_SECRET=<m365Copilot secret for dev>`.
3. Open this folder in VS Code, sign in to Microsoft 365 in Agents Toolkit, and select **Lifecycle > Provision**
   (environment `dev`).
4. In https://m365.cloud.microsoft/chat open **Birdview PSA-dev** and ask "Who am I in Birdview?".

`env/.env.*` other than the samples are git-ignored: provision writes our tenant's app and registration IDs
there, and the `.user` files hold the secret.
