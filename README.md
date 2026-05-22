# copilot-skill-acli-jira

GitHub Copilot skill voor de officiële **Atlassian CLI (`acli`)** op Windows — installeren, authenticeren en Jira issues beheren vanuit de terminal.

## Installeren

Kopieer de skill naar je persoonlijke Copilot skills-map:

```powershell
$dest = "$env:USERPROFILE\.copilot\skills\acli-jira"
New-Item -ItemType Directory -Path $dest -Force | Out-Null
Copy-Item ".github\skills\acli-jira\*" $dest -Recurse
```

Of zet de map `.github/skills/acli-jira/` direct in je teamrepo — dan is `/acli-jira` automatisch beschikbaar voor iedereen die de repo opent in VS Code met Copilot.

## Gebruik

Typ `/acli-jira` in GitHub Copilot Chat. De skill begeleidt je door:
- Installatie via `winget`
- Authenticatie via OAuth (browser) of API token
- Issues ophalen, aanmaken, bewerken en transitie
- JQL-queries en scripting

## Inhoud

| Bestand | Beschrijving |
|---|---|
| `SKILL.md` | Volledige workflow en procedures |
| `commands-reference.md` | Compleet overzicht van alle commando's en JQL-voorbeelden |
