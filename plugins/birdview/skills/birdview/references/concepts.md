# Birdview concepts

Background for explaining Birdview data. The tools return the raw values; use this to explain them, not to compute numbers the tools don't return.

## Why can't I see or change X

- Access has two layers: the user's global role (account-wide permissions) and their permissions in each project (as a project member). A user can see a project but not edit its tasks, or edit tasks but not see money.
- Money fields (rates, costs, billing amounts, budgets) need billing access and a plan that includes financials. When hidden, they come back empty (null), not 0.
- Client (guest) users belong to one customer and only see that customer's people.
- There is no per-task privacy: who can see a task follows from the project.
- Approving or unapproving time needs the project permission to edit all time entries, and approval exists only on some plans.

When a read comes back empty or forbidden, say the user may not have access rather than claiming the data doesn't exist.

## Financial terms

- **Billable vs non-billable:** billable work and expenses are what the customer is charged for. Non-billable time still costs the company.
- **Billing (revenue):** billable work and billable expenses only.
- **Internal cost:** all work at internal rates (billable and non-billable) plus expenses.
- **Profit:** billing minus internal cost.
- **Planned vs actual expenses:** planned expenses are a forecast and are kept separate from actual ones.
- **Rates:** billing rates and internal rates usually come from the person's job role or a rate card, not from the task.

## Capacity and time off

- Working hours per day and whether weekends count are account settings. Don't assume 8 hours or a Monday–Friday week; ask if it matters.
- Time off can be a full or half day, and time-off types are paid or unpaid.
- Use `birdview_get_team_availability` and `birdview_get_user_workload` for capacity questions, and say which period and assumptions you used.
