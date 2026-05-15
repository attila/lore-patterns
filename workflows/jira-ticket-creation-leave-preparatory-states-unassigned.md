---
tags: [jira, atlassian, workflow, ticket-creation]
---

# Jira ticket creation — leave preparatory states unassigned

When creating Jira tickets that land in preparatory states (Backlog, Refining,
To Do), leave the assignee blank. Do not assign by default.

**Why:** Assigning a ticket implies ownership before the work is picked up.
Premature assignment misrepresents queue state and forces another ticket update
when the actual owner emerges.

**How to apply:** Only set an assignee on new tickets when (a) the user
explicitly requests it, or (b) the ticket is being created directly into an
active state (In Progress, In Review). Default to no assignee otherwise.
