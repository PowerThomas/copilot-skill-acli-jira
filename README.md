# copilot-skill-acli-jira

GitHub Copilot skill for the official **Atlassian CLI (`acli`)** on Windows — install, authenticate, and manage Jira issues from the terminal.

## Installation

Copy the skill to your personal Copilot skills folder:

```powershell
$dest = "$env:USERPROFILE\.copilot\skills\acli-jira"
New-Item -ItemType Directory -Path $dest -Force | Out-Null
Copy-Item ".github\skills\acli-jira\*" $dest -Recurse
```

Or place the `.github/skills/acli-jira/` folder directly in your team repository — then `/acli-jira` is automatically available for everyone who opens the repo in VS Code with Copilot.

## Usage

Type `/acli-jira` in GitHub Copilot Chat. The skill guides you through:
- Installation via `winget`
- Authentication via OAuth (browser) or API token
- Retrieving, creating, editing, and transitioning issues
- JQL queries and scripting

## Contents

| File | Description |
|---|---|
| `SKILL.md` | Full workflow and procedures |
| `commands-reference.md` | Complete reference for all commands and JQL examples |
