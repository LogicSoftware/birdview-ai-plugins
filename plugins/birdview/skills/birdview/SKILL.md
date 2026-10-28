---
name: birdview
description: Work with Birdview PSA (formerly Easy Projects). Use when the user asks about their projects, tasks, time logs, timesheets, workload, team availability, PTO, customers, portfolios, expenses or payments, or wants to create or update projects, tasks, time logs, customers or custom fields in Birdview.
---

# Birdview PSA

Use the Birdview connector's `birdview_*` tools. They act as the signed-in user, so results and changes follow that user's Birdview permissions.

## If the tools are missing

The Birdview connector isn't connected yet. Tell the user to connect it in the Birdview PSA plugin:

- Claude: **Customize > Plugins > Birdview PSA**, **Connectors** tab, **Connect**.
- ChatGPT or Codex: open the Birdview PSA plugin and sign in to its Birdview connector.

On the Birdview page they enter their account name (the subdomain they use to open Birdview, e.g. `acme` for `acme.go.easyprojects.net`) and sign in.

## Vocabulary

- Space → portfolio (optional, can nest) → project → task → subtask. Subtasks are tasks with a parent. The Birdview app calls tasks "activities".
- Role (shown in the app as "access level") = what a user may do. Job role = the user's function on a project (Developer, Designer). Don't mix them up.
- Each project uses a workflow, and the workflow defines its statuses, so status names differ between projects. When setting a status, use one that tasks in the same project already have (from `birdview_search_tasks`); if unsure, ask.
- Assignees are project members. Assigning someone outside the project adds them as a member, which only works if the user can manage project members. Assignees can also be **unnamed resources**: placeholders for a person not yet chosen.
- Customers are client companies. A user can be linked to one customer.

## Reading

- Prefer the composite tools for overviews: `birdview_get_my_work`, `birdview_get_project_health`, `birdview_get_project_context`, `birdview_get_task_context`, `birdview_get_user_workload`, `birdview_get_team_availability`.
- Only the `birdview_search_*` tools take `skip`. When one returns `hasMore: true`, call it again with `skip` = `nextSkip`. When a `birdview_list_*` tool returns `hasMore: true`, narrow it if you can (only `birdview_list_users` takes `textSearch`), otherwise tell the user the list may be incomplete.
- The connector decides "open", "completed" and "overdue" itself. A task is completed if it has a completion date or 100% progress, or its workflow status is marked as completed. When a status carries no such mark, the connector falls back to the status name containing "complete", "done" or "closed", so mention this when counts look off.
- The connector judges "today" and "overdue" in UTC, and the default "this week" runs Monday–Sunday in UTC. Near midnight or far from UTC, the user's local day can differ; say so when it matters.
- A hidden or unknown value is left out of the result. Report it as not available, never as 0.

## Hours and estimates

- A task has an estimate and hours left (remaining work), and each assignee has their own estimate and hours left. Logging time reduces hours left.
- "On budget" in `birdview_get_project_health` compares the project's logged hours with the project's own estimated hours. It is not a financial verdict; don't present it as one.
- When the project has no estimate (missing or 0), health can't answer. If its tasks have estimates, add them up from `birdview_search_tasks`, compare with logged hours, and say that's how you got the total.

## Names to IDs

Users name things ("project Atlas", "the onboarding task", "Maria"); most tools take IDs.

- Search first (`birdview_search_projects`, `birdview_search_tasks`, `birdview_list_users`, `birdview_list_customers`) and use the ID from the result.
- Exactly one match: use it, and name it in the reply so the user can spot a wrong pick.
- Several matches: list them (name plus a distinguishing field such as project, customer or dates) and ask which one. Don't pick one yourself.
- No match: retry with a shorter fragment and narrow by project, portfolio or customer. `nameContains` filters only the rows the server fetched; a result marked `incompleteFilter` may miss items. For people, use `birdview_list_users` with `textSearch`. If there's still no match, say so and ask; don't create a new item instead.
- Some write tools take names: space (`birdview_create_project`), category (`birdview_patch_task`, `birdview_bulk_patch_tasks`), task type (`birdview_create_task`, `birdview_bulk_create_tasks`; default "Task"), and portfolio and assignees (`birdview_bulk_create_tasks` only). Pass the exact name from a list or search result, and get category and task type labels from `birdview_list_system_lists`.
  - A category that doesn't match is silently dropped.
  - A task type, space or assignee name that doesn't match fails the call.
  - A portfolio name with several exact matches silently takes the first.
  - Check the result to confirm the value was applied.
- Never guess or reuse an ID that didn't come from a tool result in this conversation.

## Changing data

- Confirm with the user before every create, update or delete, and say what will change. The server doesn't ask: single writes and `birdview_delete_timelog` run immediately.
- For bulk changes, always call `birdview_bulk_patch_tasks` / `birdview_bulk_create_tasks` with `dryRun: true` first and show the preview. Up to 50 items run immediately without one. Above 50 the server returns a preview until you pass `confirm: true`; pass it only after the user approves.
- `birdview_update_custom_field` changes the field for the whole account and replaces its full definition. Read the current definition first (`birdview_get_custom_field_definitions`) and include every existing picklist value; values you leave out are deleted. Tell the user that every project or task using the field is affected.
- When a patch returns `warnings`, Birdview adjusted or kept a date, or changed the duration (because of a dependency, a subtask rollup, or a project rolling up from its tasks). Tell the user the value that was actually saved. If none of the requested dates were applied, the patch returns an error instead.
- If Birdview refuses a change, explain the likely reason (see "Why can't I see or change X" in `references/concepts.md`) instead of retrying or working around it.
- The connector can't move a task to another parent, move a project to another space, or assign user skills. It can't create or change expenses, payments or time off, or delete tasks or projects. Suggest doing these in the Birdview app.

## Time logs

- Before `birdview_create_timelog`, settle three things: the task, the date and whether the time is billable.
  - The tool always sends billable = false unless you pass it, ignoring the task's billing settings. Ask unless the user said.
  - Pass `entryDate` as the user's local date; the default is today in UTC.
- `birdview_update_timelog` keeps every field you don't pass.
- An invoiced log is always billable.
- Birdview can lock a log against editing or deleting. Reasons: it is invoiced or approved, it is waiting for approval, its period is frozen, or its task is closed. Which rules apply depends on the account's plan and approval settings. Users with wider permissions can sometimes still edit.
- Accounts can block adding, editing or deleting time dated more than a set number of days or months back. The error names the cutoff date; pass it on.

For permissions, financial terms and capacity, read `references/concepts.md` when a question touches them.

## Replies

Summarize results in plain language with the key fields (name, owner, dates, hours, status). Don't paste raw tool output. Results carry no links to individual projects or tasks (`birdview_who_am_i` returns only the account's base `appUrl`), so identify items by name (task name plus its project) and don't build deep links.
