---
name: birdview
description: Work with Birdview PSA (formerly Easy Projects). Use when the user asks about their projects, tasks, time logs, timesheets, workload, team availability, PTO, customers, portfolios, expenses or payments, or wants to create or update any of them in Birdview.
---

# Birdview PSA

Use the Birdview connector's `birdview_*` tools. They act as the signed-in user, so results and changes follow that user's Birdview permissions.

## If the tools are missing

The connector isn't connected yet. Tell the user to add it under **Settings > Connectors > Add custom connector** with their account URL:

```
https://<account>.go.easyprojects.net/services/mcp
```

`<account>` is the subdomain they use to open Birdview. They then sign in with their Birdview account.

## Reading

- Start with `birdview_who_am_i` when the request depends on who the user is ("my tasks", "my hours").
- Prefer the composite tools for overviews: `birdview_get_my_work`, `birdview_get_project_health`, `birdview_get_project_context`, `birdview_get_task_context`, `birdview_get_user_workload`, `birdview_get_team_availability`.
- Search results are paged. When `hasMore` is true, call again with `skip` = `nextSkip` instead of assuming the list is complete.

## Names to IDs

Users name things ("project Atlas", "the onboarding task", "Maria"); tools take IDs.

- Search first (`birdview_search_projects`, `birdview_search_tasks`, `birdview_list_users`, `birdview_list_customers`) and use the ID from the result.
- Exactly one match: use it, and name it in the reply so the user can spot a wrong pick.
- Several matches: list them (name plus a distinguishing field such as project, customer or dates) and ask which one. Don't pick one yourself.
- No match: say so and ask; don't create a new item instead.
- Never guess or reuse an ID that didn't come from a tool result in this conversation.

## Changing data

- Confirm with the user before creating, updating or deleting anything, and say what will change.
- For bulk changes, call `birdview_bulk_patch_tasks` / `birdview_bulk_create_tasks` with `dryRun: true` first and show the preview. Above 50 matched items the server requires `confirm: true`; set it only after the user approves the preview.
- When a patch returns `warnings`, a date was clamped by a dependency or rollup. Tell the user the value that was actually saved.
- Not supported: moving tasks to another parent, moving a project to another space, assigning user skills. Say so and suggest doing it in the Birdview app; don't try workarounds.

## Replies

Summarize results in plain language with the key fields (name, owner, dates, hours, status). Don't paste raw tool output.

Link every project and task you mention to Birdview. Take `appUrl` from `birdview_who_am_i` (call it once per conversation) and build:

- Project: `{appUrl}/activities/project/{projectId}`
- Task: `{appUrl}/activities/activity/{taskId}`

Use them as Markdown links on the item name, e.g. `[Website redesign](https://acme.go.easyprojects.net/activities/project/8)`. If `who_am_i` returns no `appUrl`, leave the names unlinked; don't guess the domain.
