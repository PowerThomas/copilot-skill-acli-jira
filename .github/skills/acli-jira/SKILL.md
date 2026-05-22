---
name: acli-jira
description: "Installeer, authenticeer en gebruik de officiële Atlassian CLI (acli) voor Jira Cloud op Windows. Gebruik wanneer: Jira issues ophalen, aanmaken, bewerken of status wijzigen via de terminal; acli installeren op Windows via winget; acli authenticeren met Jira Cloud via OAuth of API token; work items zoeken met JQL; sprints, epics of projecten beheren vanuit de terminal."
argument-hint: "Optioneel: beschrijf wat je wil doen, bijv. 'issues ophalen uit project PROJ' of 'nieuwe bug aanmaken'"
---

# Atlassian CLI (acli) voor Jira Cloud

Officiële Atlassian CLI installeren, authenticeren en gebruiken voor dagelijks Jira-werk vanuit de terminal.

## Wanneer gebruiken

- Jira issues ophalen, aanmaken, bewerken, of status wijzigen via de terminal
- Acli installeren op een Windows-machine
- Acli authenticeren met een Jira Cloud-omgeving
- Work items zoeken met JQL-queries
- Sprints, boards, projecten of epics beheren vanuit PowerShell/terminal
- Automatiseren van terugkerende Jira-taken (CI/CD, scripts)

## Vereisten

- Windows met `winget` beschikbaar (standaard aanwezig op Windows 10/11)
- Toegang tot een Jira Cloud-omgeving (bijv. `jouwbedrijf.atlassian.net`)
- Atlassian-account met toegang tot het Jira-project

---

## Procedure

### Stap 1 — Controleer of acli al geïnstalleerd is

```powershell
acli --version
```

Als het commando herkend wordt: ga naar **Stap 3 (Authenticatie)**.  
Als je `not recognized` ziet: ga naar **Stap 2 (Installatie)**.

---

### Stap 2 — Installeer acli via winget

```powershell
winget install Atlassian.AtlassianCLI --accept-source-agreements --accept-package-agreements
```

Na installatie is het PATH bijgewerkt, maar de huidige terminalsessie herkent `acli` nog niet.  
Vernieuw het PATH **zonder de terminal te sluiten**:

```powershell
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")
acli --version   # Moet nu werken
```

> Nieuwe terminalsessies herkennen `acli` automatisch.

---

### Stap 3 — Authenticeer bij Jira Cloud

#### Optie A: OAuth via browser (aanbevolen, geen token nodig)

```powershell
acli jira auth login --web
```

De browser opent automatisch. Log in bij Atlassian, klik **Accept**, ga terug naar de terminal en selecteer je site (bijv. `jouwbedrijf.atlassian.net`) met Enter.

#### Optie B: API token

1. Ga naar: https://id.atlassian.com/manage-profile/security/api-tokens → **Create API token**
2. Kopieer het token en voer uit:

```powershell
echo <jouw-token> | acli jira auth login --site "jouwbedrijf.atlassian.net" --email "jij@bedrijf.com" --token
```

Of lees het token uit een bestand (veiliger):

```powershell
acli jira auth login --site "jouwbedrijf.atlassian.net" --email "jij@bedrijf.com" --token < token.txt
```

#### Controleer authenticatie

```powershell
acli jira auth status
```

---

### Stap 4 — Gebruik: veelgebruikte commando's

Zie [commands-reference.md](./commands-reference.md) voor het volledige overzicht.

#### Issues ophalen

```powershell
# Recente work items in een project
acli jira workitem list --project "PROJ"

# Filteren met JQL
acli jira workitem list --jql "project = PROJ AND status = 'In Progress' AND assignee = currentUser()"

# Specifieke issue bekijken
acli jira workitem get --key "PROJ-123"

# Output als JSON (voor scripting)
acli jira workitem list --project "PROJ" --output json
```

#### Issues aanmaken

```powershell
# Interactief (vraagt om details)
acli jira workitem create

# Direct met alle parameters
acli jira workitem create --summary "Nieuwe bug" --project "PROJ" --type "Bug"

# Met meer details
acli jira workitem create \
  --summary "Taak omschrijving" \
  --project "PROJ" \
  --type "Task" \
  --assignee "gebruiker@bedrijf.com" \
  --label "backend,urgent"

# Vanuit een tekstbestand
acli jira workitem create --from-file "issue.txt" --project "PROJ" --type "Story"
```

#### Issues bewerken

```powershell
# Samenvatting aanpassen
acli jira workitem edit --key "PROJ-123" --summary "Bijgewerkte samenvatting"

# Toewijzen aan iemand
acli jira workitem edit --key "PROJ-123" --assignee "gebruiker@bedrijf.com"

# Meerdere issues tegelijk bewerken via JQL
acli jira workitem edit --jql "project = PROJ AND status = 'To Do'" --assignee "gebruiker@bedrijf.com" --yes
```

#### Status wijzigen (transitie)

```powershell
acli jira workitem transition --key "PROJ-123" --status "In Progress"
acli jira workitem transition --key "PROJ-123" --status "Done"

# Meerdere issues via JQL
acli jira workitem transition --jql "project = PROJ AND assignee = currentUser()" --status "In Review" --yes
```

#### Projecten, boards & sprints

```powershell
# Beschikbare projecten
acli jira project list

# Boards in een project
acli jira board list --project "PROJ"

# Sprints
acli jira sprint list --project "PROJ"
```

---

### Hulp & help

```powershell
# Overzicht van alle Jira-commando's
acli jira --help

# Help per subcommando
acli jira workitem --help
acli jira workitem create --help
```

---

## Probleemoplossing

| Probleem | Oplossing |
|---|---|
| `acli: not recognized` na installatie | Vernieuw PATH: `$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine") + ";" + [System.Environment]::GetEnvironmentVariable("Path","User")` |
| Browser opent niet bij `--web` | Probeer API token-methode (Optie B) |
| `unauthorized` bij commando's | Voer `acli jira auth login --web` opnieuw uit |
| Site niet gevonden | Controleer de volledige URL: `jouwbedrijf.atlassian.net` (zonder `https://`) |

---

## Voor collega's: skill delen

Zet de map `acli-jira/` in de repository onder `.github/skills/acli-jira/` zodat het team hem automatisch beschikbaar heeft in VS Code Copilot Chat via `/acli-jira`.
