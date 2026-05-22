---
name: acli-jira
description: "Install, authenticate, and use the official Atlassian CLI (acli) for Jira Cloud on Windows. Use when: retrieving, creating, editing, or transitioning Jira issues from the terminal; installing acli on Windows via winget; authenticating acli with Jira Cloud via OAuth or API token; searching work items with JQL; managing sprints, epics, or projects from the terminal."
argument-hint: "Optional: describe what you want to do, e.g. 'list issues in project PROJ' or 'create a new bug'"
---

# Atlassian CLI (acli) for Jira Cloud

Install, authenticate, and use the official Atlassian CLI for day-to-day Jira work from the terminal.

## When to Use

- Retrieve, create, edit, or transition Jira issues from the terminal
- Install acli on a Windows machine
- Authenticate acli with a Jira Cloud environment
- Search work items with JQL queries
- Manage sprints, boards, projects, or epics from PowerShell/terminal
- Automate repetitive Jira tasks (CI/CD, scripts)

## Prerequisites

- Windows with `winget` available (built-in on Windows 10/11)
- Access to a Jira Cloud environment (e.g. `yourcompany.atlassian.net`)
- Atlassian account with access to the Jira project

---

## Procedure

### Step 1 — Check if acli is already installed

```powershell
acli --version
```

If the command is recognized: skip to **Step 3 (Authentication)**.  
If you see `not recognized`, first try refreshing the PATH (acli may be installed but not yet on the current session's PATH):

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
acli --version
```

If it still fails: continue to **Step 2 (Installation)**.

---

### Step 2 — Install acli via winget

```powershell
winget install Atlassian.AtlassianCLI --accept-source-agreements --accept-package-agreements
```

After installation the PATH is updated, but the current terminal session does not yet recognize `acli`.  
Refresh the PATH **without closing the terminal**:

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
acli --version   # Should work now
```

> New terminal sessions will recognize `acli` automatically.

---

### Step 3 — Authenticate with Jira Cloud

#### Option A: OAuth via browser (recommended, no token required)

```powershell
acli jira auth login --web
```

The browser opens automatically. Log in to Atlassian, click **Accept**, return to the terminal, and select your site (e.g. `yourcompany.atlassian.net`) with Enter.

#### Option B: API token

1. Go to: https://id.atlassian.com/manage-profile/security/api-tokens → **Create API token**
2. Copy the token and run:

```powershell
echo <your-token> | acli jira auth login --site "yourcompany.atlassian.net" --email "you@company.com" --token
```

Or read the token from a file (more secure):

```powershell
acli jira auth login --site "yourcompany.atlassian.net" --email "you@company.com" --token < token.txt
```

#### Verify authentication

```powershell
acli jira auth status
```

---

### Step 4 — Common commands

See [commands-reference.md](./commands-reference.md) for the full reference.

#### Retrieve issues

```powershell
# Search work items in a project
acli jira workitem search --project "PROJ"

# Filter with JQL
acli jira workitem search --jql "project = PROJ AND status = 'In Progress' AND assignee = currentUser()"

# View a specific issue (key is a positional argument)
acli jira workitem view "PROJ-123"

# View as JSON (for scripting)
acli jira workitem view "PROJ-123" --json

# Capture view output to file (bypasses the built-in pager)
acli jira workitem view "PROJ-123" --json --fields "*all" | Out-File -FilePath "PROJ-123.json" -Encoding utf8

# Search and output as JSON
acli jira workitem search --project "PROJ" --json
```

#### Create issues

```powershell
# Interactive (prompts for details)
acli jira workitem create

# Direct with all parameters
acli jira workitem create --summary "New bug" --project "PROJ" --type "Bug"

# With additional details
acli jira workitem create `
  --summary "Task description" `
  --project "PROJ" `
  --type "Task" `
  --assignee "user@company.com" `
  --label "backend,urgent"

# From a text file
acli jira workitem create --from-file "issue.txt" --project "PROJ" --type "Story"
```

#### Edit issues

```powershell
# Update summary
acli jira workitem edit --key "PROJ-123" --summary "Updated summary"

# Assign to someone
acli jira workitem edit --key "PROJ-123" --assignee "user@company.com"

# Edit multiple issues at once via JQL
acli jira workitem edit --jql "project = PROJ AND status = 'To Do'" --assignee "user@company.com" --yes
```

#### Transition status

```powershell
acli jira workitem transition --key "PROJ-123" --status "In Progress"
acli jira workitem transition --key "PROJ-123" --status "Done"

# Multiple issues via JQL
acli jira workitem transition --jql "project = PROJ AND assignee = currentUser()" --status "In Review" --yes
```

#### Comments

```powershell
# List comments (note: body is truncated in the output)
acli jira workitem comment list --key "PROJ-123"

# Get full comment body — via workitem view JSON
acli jira workitem view "PROJ-123" --json --fields "*all" | Out-File -FilePath "PROJ-123.json" -Encoding utf8
# Body is located at: fields.comment.comments[].body (ADF format)

# Add a comment (plain text — no formatting in Jira)
acli jira workitem comment create --key "PROJ-123" --body "Text here"

# Add a comment with formatting (ADF JSON — see template below)
acli jira workitem comment create --key "PROJ-123" --body-file "comment.json"

# Update a comment
acli jira workitem comment update --key "PROJ-123" --id "123456" --body-file "comment.json"

# Delete a comment
acli jira workitem comment delete --key "PROJ-123" --id "123456"
```

> **Tip — formatting**: plain text (`-` dashes) render as literal text in Jira, not as real bullets.  
> Use **ADF JSON** for proper formatting (see template below).

##### ADF template — bullet list comment

Save as `comment.json`, use `--body-file comment.json`, then delete the file:

```json
{
  "version": 1,
  "type": "doc",
  "content": [
    {
      "type": "paragraph",
      "content": [{ "type": "text", "text": "Intro tekst hier" }]
    },
    {
      "type": "bulletList",
      "content": [
        {
          "type": "listItem",
          "content": [
            {
              "type": "paragraph",
              "content": [{ "type": "text", "text": "First bullet" }]
            }
          ]
        },
        {
          "type": "listItem",
          "content": [
            {
              "type": "paragraph",
              "content": [{ "type": "text", "text": "Second bullet" }]
            }
          ]
        }
      ]
    }
  ]
}
```

> Create the file using the `create_file` tool — do **not** use PowerShell here-strings (see Troubleshooting).

---

#### Projects, boards & sprints

```powershell
# List available projects
acli jira project list

# Boards in a project
acli jira board list --project "PROJ"

# Sprints
acli jira sprint list --project "PROJ"
```

---

### Help

```powershell
# All Jira commands
acli jira --help

# Help per subcommand
acli jira workitem --help
acli jira workitem create --help
```

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `acli: not recognized` (after install or in new terminal) | Refresh PATH: `$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")` |
| Output is cut off or requires pressing `q` | Pipe `view` to a file to bypass the pager: `acli jira workitem view "PROJ-123" --json \| Out-File issue.json` |
| Browser does not open with `--web` | Use the API token method (Option B) |
| `unauthorized` when running commands | Re-run `acli jira auth login --web` |
| Site not found | Check the full URL: `yourcompany.atlassian.net` (without `https://`) |
| Comment body truncated in `comment list` | Use `acli jira workitem view "PROJ-123" --json --fields "*all"` and read `fields.comment.comments[].body` |
| Comment bullet points do not render in Jira | Use ADF JSON format via `--body-file comment.json` instead of plain text with `-` dashes |
| PowerShell here-string (`@"..."@`) appears to be waiting but has already executed | The `>>` prompts are misleading — commands after the here-string may have already run. Create files using the `create_file` tool instead of here-strings in the terminal |

---

## Sharing this skill with colleagues

Place the `acli-jira/` folder in your team repository under `.github/skills/acli-jira/` — then `/acli-jira` is automatically available for everyone who opens the repo in VS Code with Copilot.
