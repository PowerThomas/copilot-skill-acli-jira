# acli Jira — Commands Reference

Full reference for all available `acli jira` commands.

## Work Items

| Goal | Command |
|---|---|
| List / search work items | `acli jira workitem search --project "PROJ"` |
| Search with JQL | `acli jira workitem search --jql "<jql-query>"` |
| View details | `acli jira workitem view "PROJ-123"` |
| Create (interactive) | `acli jira workitem create` |
| Create (direct) | `acli jira workitem create --summary "..." --project "PROJ" --type "Bug"` |
| Edit | `acli jira workitem edit --key "PROJ-123" --summary "..."` |
| Transition status | `acli jira workitem transition --key "PROJ-123" --status "Done"` |
| Assign | `acli jira workitem edit --key "PROJ-123" --assignee "user@company.com"` |
| Delete | `acli jira workitem delete --key "PROJ-123"` |

## Work item types (--type)

Common values: `Bug`, `Task`, `Story`, `Epic`, `Sub-task`  
Exact names are project-dependent — check via the Jira UI or: `acli jira field list`

## JQL Examples

```jql
# Everything assigned to you
assignee = currentUser()

# Open issues in a project
project = PROJ AND status != Done

# High priority, created this week
project = PROJ AND priority = High AND created >= startOfWeek()

# Issues with no assignee
project = PROJ AND assignee is EMPTY

# All issues in the active sprint
project = PROJ AND sprint in openSprints()

# Specific label
project = PROJ AND labels = "backend"
```

## Output Formats

```powershell
# Default (table)
acli jira workitem search --project "PROJ"

# JSON output for search
acli jira workitem search --project "PROJ" --json

# JSON output for a single issue
acli jira workitem view "PROJ-123" --json

# All fields (view)
acli jira workitem view "PROJ-123" --json --fields "*all"

# Capture to file (bypasses the built-in pager)
acli jira workitem view "PROJ-123" --json --fields "*all" | Out-File -FilePath "PROJ-123.json" -Encoding utf8

# Process JSON with PowerShell
acli jira workitem search --project "PROJ" --json | ConvertFrom-Json | Select-Object key, fields
```

## Projects & Boards

```powershell
acli jira project list
acli jira project get --key "PROJ"
acli jira board list --project "PROJ"
```

## Sprints

```powershell
acli jira sprint list --project "PROJ"
acli jira sprint list --board <board-id>
```

## Filters & Dashboards

```powershell
acli jira filter list
acli jira filter get --id 10001
acli jira dashboard list
```

## Manage Authentication

```powershell
# Check login status
acli jira auth status

# Re-authenticate (OAuth)
acli jira auth login --web

# Log out
acli jira auth logout
```

## Scripting Example: Bulk Transition

```powershell
# Move all your 'To Do' issues to 'In Progress'
acli jira workitem transition `
  --jql "project = PROJ AND status = 'To Do' AND assignee = currentUser()" `
  --status "In Progress" `
  --yes
```

## Documentation

- Official docs: https://developer.atlassian.com/cloud/acli/
- Commands reference: https://developer.atlassian.com/cloud/acli/reference/commands/jira/
- Getting started: https://developer.atlassian.com/cloud/acli/guides/how-to-get-started/
