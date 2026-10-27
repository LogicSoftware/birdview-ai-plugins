---
name: birdview
description: Work with Birdview PSA (formerly Easy Projects). Use when the user asks about their projects, tasks, time logs, timesheets, workload, team availability, PTO, customers, portfolios, expenses or payments, or wants to create or update any of them in Birdview.
---

# Birdview PSA

Use the Birdview connector's `birdview_*` tools. They act as the signed-in user, so results and changes follow that user's Birdview permissions.

## If the tools are missing

The Birdview connector isn't connected yet. Tell the user to open the Birdview PSA plugin's **Connectors** tab (**Customize > Plugins > Birdview PSA**) and select **Connect**. On the Birdview page they enter their account name (the subdomain they use to open Birdview, e.g. `acme` for `acme.go.easyprojects.net`) and sign in.

## Vocabulary

- Space → portfolio (optional, can nest) → project → task → subtask. Subtasks are tasks with a parent. The Birdview app calls tasks "activities".
- Role = what a user may do (permissions). Job role = what they do (and their billing and internal rates). Don't mix them up.
- Each project uses a workflow, and the workflow defines its statuses, so status names can differ between projects. Resolve statuses per project.
- Tasks can only be assigned to project members. Assignees can also be unnamed placeholder resources.
- Customers are client accounts. Client (guest) users belong to one customer.

## Reading

- Start with `birdview_who_am_i` when the request depends on who the user is ("my tasks", "my hours").
- Prefer the composite tools for overviews: `birdview_get_my_work`, `birdview_get_project_health`, `birdview_get_project_context`, `birdview_get_task_context`, `birdview_get_user_workload`, `birdview_get_team_availability`.
- Search results are paged. When `hasMore` is true, call again with `skip` = `nextSkip` instead of assuming the list is complete.
- A status's `completed` flag (when present) tells whether it counts as done in its workflow. Use it, not the status name, to decide what is open, done or overdue.
- Dates have no time zone; they are in the account's local time. "Overdue" means the due date is before today and the task isn't completed.
- A null hours or money field means hidden or unknown, not zero. Never report it as 0.

## Hours and estimates

- Hours are decimal (0.25 = 15 min). A task has an estimate, hours left (remaining work) and per-assignee estimates.
- "On budget" in `birdview_get_project_health` compares logged hours with estimated hours. It is not a financial verdict; don't present it as one.
- If a project shows no estimate but its tasks have estimates, add up the task estimates from `birdview_search_tasks` and say that's how you got the total.

## Names to IDs

Users name things ("project Atlas", "the onboarding task", "Maria"); tools take IDs.

- Search first (`birdview_search_projects`, `birdview_search_tasks`, `birdview_list_users`, `birdview_list_customers`) and use the ID from the result.
- Exactly one match: use it, and name it in the reply so the user can spot a wrong pick.
- Several matches: list them (name plus a distinguishing field such as project, customer or dates) and ask which one. Don't pick one yourself.
- No match: before saying nothing was found, retry with a shorter fragment and narrow by project, portfolio or customer (name filters only scan the first page of results the server returns). For people, use `birdview_list_users` with `textSearch`. If there's still no match, say so and ask; don't create a new item instead.
- Never guess or reuse an ID that didn't come from a tool result in this conversation.

## Changing data

- Confirm with the user before creating, updating or deleting anything, and say what will change.
- For bulk changes, call `birdview_bulk_patch_tasks` / `birdview_bulk_create_tasks` with `dryRun: true` first and show the preview. Above 50 matched items the server requires `confirm: true`; set it only after the user approves the preview.
- When a patch returns `warnings`, a date was clamped by a dependency or rollup. Tell the user the value that was actually saved.
- If Birdview refuses a change, explain the likely reason (see "Why can't I see or change X" in `references/concepts.md`) instead of retrying or working around it.

## Time logs

- Before `birdview_create_timelog`, settle three things: the task, the date and whether the time is billable.
  - The tool defaults to non-billable, so ask unless the user said.
  - Pass `entryDate` as the user's local date: the default is today in UTC, which can land on the wrong day.
- Billed time is always billable. A log that is billed or approved is locked: it can't be deleted, and editing it may be refused.
- Some accounts block adding or editing time more than a set number of days in the past. If a back-dated entry fails, mention that setting.
- Not supported: moving tasks to another parent, moving a project to another space, assigning user skills. Say so and suggest doing it in the Birdview app; don't try workarounds.

For permissions, financial terms and capacity, read `references/concepts.md` when a question touches them.

## Replies

Summarize results in plain language with the key fields (name, owner, dates, hours, status). Don't paste raw tool output.

Link every project and task you mention to Birdview. Take `appUrl` from `birdview_who_am_i` (call it once per conversation) and build:

- Project: `{appUrl}/activities/project/{projectId}`
- Task: `{appUrl}/activities/activity/{taskId}`

Put the link on the item's name: `[Website redesign](https://acme.go.easyprojects.net/activities/project/8)`. Never show a bare URL, an ID-only link text ("#8", "link", "open") or a separate link column. In tables, the name cell is the link:

| Task | Owner | Due | Status |
|------|-------|-----|--------|
| [Kickoff meeting](https://acme.go.easyprojects.net/activities/activity/49) | Maria Lee | Oct 24 | In progress |

If `who_am_i` returns no `appUrl`, leave the names unlinked; don't guess the domain.
