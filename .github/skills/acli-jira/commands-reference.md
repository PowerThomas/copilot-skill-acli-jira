# acli Jira — Commando's Referentie

Volledig overzicht van alle beschikbare `acli jira` commando's.

## Work Items

| Doel | Commando |
|---|---|
| Lijst ophalen | `acli jira workitem list --project "PROJ"` |
| Zoeken met JQL | `acli jira workitem list --jql "<jql-query>"` |
| Details bekijken | `acli jira workitem get --key "PROJ-123"` |
| Aanmaken (interactief) | `acli jira workitem create` |
| Aanmaken (direct) | `acli jira workitem create --summary "..." --project "PROJ" --type "Bug"` |
| Bewerken | `acli jira workitem edit --key "PROJ-123" --summary "..."` |
| Status wijzigen | `acli jira workitem transition --key "PROJ-123" --status "Done"` |
| Toewijzen | `acli jira workitem edit --key "PROJ-123" --assignee "user@bedrijf.com"` |
| Verwijderen | `acli jira workitem delete --key "PROJ-123"` |

## Work item types (--type)

Veelgebruikte waarden: `Bug`, `Task`, `Story`, `Epic`, `Sub-task`  
Exacte namen zijn projectafhankelijk — controleer via Jira UI of: `acli jira field list`

## JQL voorbeelden

```jql
# Alles toegewezen aan jou
assignee = currentUser()

# Open issues in een project
project = PROJ AND status != Done

# Hoge prioriteit, aangemaakt deze week
project = PROJ AND priority = High AND created >= startOfWeek()

# Issues zonder toewijzing
project = PROJ AND assignee is EMPTY

# Alle issues in de actieve sprint
project = PROJ AND sprint in openSprints()

# Specifiek label
project = PROJ AND labels = "backend"
```

## Output formaten

```powershell
# Standaard (tabel)
acli jira workitem list --project "PROJ"

# JSON (voor scripting of piping)
acli jira workitem list --project "PROJ" --output json

# JSON verwerken met PowerShell
acli jira workitem list --project "PROJ" --output json | ConvertFrom-Json | Select-Object key, fields
```

## Projecten & Boards

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

## Authenticatie beheren

```powershell
# Inlogstatus controleren
acli jira auth status

# Opnieuw inloggen (OAuth)
acli jira auth login --web

# Uitloggen
acli jira auth logout
```

## Scripting voorbeeld: bulk-transitie

```powershell
# Alle 'To Do' issues van jou naar 'In Progress'
acli jira workitem transition \
  --jql "project = PROJ AND status = 'To Do' AND assignee = currentUser()" \
  --status "In Progress" \
  --yes
```

## Documentatie

- Officiële docs: https://developer.atlassian.com/cloud/acli/
- Commando's referentie: https://developer.atlassian.com/cloud/acli/reference/commands/jira/
- Aan de slag: https://developer.atlassian.com/cloud/acli/guides/how-to-get-started/
