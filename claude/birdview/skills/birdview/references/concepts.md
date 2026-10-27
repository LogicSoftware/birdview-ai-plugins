# Birdview concepts

Background for explaining Birdview data. The tools return the raw values; use this to explain them, not to compute numbers the tools don't return. Use the app's labels (in quotes below) so the user can find the same thing in Birdview.

## Why can't I see or change X

**Projects.** A user gets access to a project in one of these ways:
- They are a project member. Each member has their own project permissions, which start from their role's defaults and can be changed per member.
- They are a member of the project's portfolio or of a parent portfolio. Portfolio access passes down to the projects inside.
- Their role grants access to all projects or all portfolios.

So one user can see a project but not edit its tasks, or edit tasks but not see money.

**Tasks.** Visibility is per task. Without the project permission to view all activities, a user sees only the tasks they created or are assigned to, plus those tasks' parents and subtasks. Editing works the same way: they can edit all tasks, only tasks assigned to them, or none.

**Time logs.** Without permission to view all time entries in a project, a user sees only their own logs. Logging time for someone else needs its own permission.

**Money.** Rates, costs, billable amounts and budgets need two things:
- The global permission "Access billing information" (on some plans the label is "Access financial information"). The same permission covers billing rates and internal rates.
- A plan that includes the feature. Some fields need specific plan features, e.g. budgets.

Hidden money fields are left out of the result, not returned as 0.

**Customers.**
- A user linked to a customer sees only that customer and the people of that customer (plus people with no customer), unless they can manage customers.
- Guest users are more limited: in people lists they see only themselves, and they see no customers.

**Approval.** There are two kinds of time-log approval:
- A simple "Approved" mark. Setting it needs the project permission to edit all time entries. Once a log is approved, its author can no longer edit or delete it.
- An approval workflow (Draft → Submitted → Approved / Rejected). It needs a plan that includes it and must be turned on for the account. The account's settings decide who approves. While a log is submitted or approved, it can't be edited.

**Expenses.** Expenses have their own project permissions. Expense approval also needs a plan that includes it.

When a result is empty or forbidden, say the user may not have access. Name the likely missing permission if the cases above point to one. Don't claim the data doesn't exist.

## Financial terms

The connector's financial tools return expenses (`birdview_search_expenses`) and payments (`birdview_search_payments`). When a metric below isn't in a tool result, explain the term and point the user to the project's financials in Birdview. Don't calculate it from partial data.

- **Billable vs non-billable.** Billable time and expenses are what the customer is charged for. Non-billable time still costs the company. Each time log has its own billable flag.
- **"Actual billable"** (billing, revenue): billable time logs plus flat-fee activities plus actual billable expenses. On a flat-fee project, it's the project's flat fee plus actual billable expenses. It counts billable work whether or not it's invoiced.
- **"Actual cost"** (internal cost): all logged time at internal rates, billable or not, plus actual expenses. If expense approval is on, only approved expenses count.
- **"Profit"** = Actual billable − Actual cost. It can be negative. **"Project profitability"** = Profit ÷ Actual billable, as a percentage.
- **"Invoiced"**: billable time and expenses marked as invoiced. Related metrics are "Total invoiced", "Percentage invoiced" and work in progress (billable not yet invoiced). Invoicing or approving a log freezes its billing rate.
- **Planned vs actual expenses.** Planned expenses are a forecast for budgets and forecasts. They never count in actual billable or actual cost, and they can't be invoiced. Planned expenses can be converted into actual ones.
- **Forecasts.**
  - "ETC" (estimate to complete) is the remaining part.
  - "EAC" (estimate at completion) = actual + ETC, for both cost and billable.
  - "Expected profit" = EAC billable − EAC cost.
  - "Project expected revenue" is a different number, based on estimated hours. Don't treat it as the same as EAC billable.
- **Budgets.**
  - "Labor budget" and "Contingency" are set on the project.
  - "Total project budget" can also include activity budgets and planned expenses, depending on project settings.
  - "Remaining budget" = budget − actual cost.
- **Project billing type:**
  - "Time & material" bills hours at a rate. The project's billing basis sets which rate: "Resource hourly rate", "Customer hourly rate", "Project hourly rate", "Activity rate" or "Rate card".
  - "Project flat fee" bills a fixed amount.
  - "Not billable".
- **Task billing.** A task can follow the project or override it with its own hourly rate, flat fee, rate card or "Not billable".
- **Rates.**
  - The internal rate (cost) always comes from the user's own rate on the date of the log.
  - Job-role rates apply through a rate card (the person's job role on that project) and to unnamed resources in planned cost.

## Capacity and time off

How Birdview defines capacity:
- **Capacity is per user.** Each user has a weekly schedule with hours for each weekday, so part-time and weekend work are possible. New users start with the account's default hours per day, Monday to Friday, but any user can differ.
- **Calendars aren't capacity.** Account and project calendars set working days for task dates. They don't set a person's capacity.
- **What reduces capacity:**
  - Time off: a full day removes the day, a half day removes half of it. Paid and unpaid types count the same.
  - Public holidays for the user's region.
- **Time off has no approval step.** Users with the right permission add it directly.
- **Workload.** In Birdview's workload views, each assignee's remaining hours on a task are spread over the task's remaining working days. Overdue work lands on today. A person is overloaded when their planned or allocated hours exceed their capacity for that day.

What the connector gives you:
- `birdview_get_team_availability` returns time off and public holidays for a period. It doesn't return hours or capacity.
- `birdview_get_user_workload` returns counts of a user's assigned, open and overdue tasks, the overdue tasks themselves, and the hours they logged in the period (default: this week). For the full task list use `birdview_search_tasks`.
- Work schedules and the account's hours per day aren't available, so the connector can't give exact capacity.

When the user asks about capacity or utilization:
- Show what the tools return: time off, holidays, assigned work and logged hours.
- If they want a number, ask for the hours per day and working days to use, or label your figure as an estimate based on an assumed schedule.
- Say that individual schedules (part-time, other working days) may differ.
