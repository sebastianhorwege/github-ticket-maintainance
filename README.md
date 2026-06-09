# github-ticket-maintainance

Zentrales Repo zum Monitoren und Verwalten von GitHub-Tickets in der Organisation **atacama-blooms-gmbh-co-kg**.

## Was dieses Repo ist

Kein statisches Ticket-Register. Stattdessen:

- **Skills** (`skills/`) – Copilot-Workflows um Tickets zu finden, zu priorisieren und aufzulisten
- **Dashboard** (`DASHBOARD.md`) – vom Agenten aktualisierte Übersicht: offene Mentions, Review-Requests, Termine
- **Instructions** (`.github/copilot-instructions.md`) – Kontext für Copilot, damit Skills automatisch geladen werden

## Schnellstart

Copilot direkt ansprechen:

```
"Mach ein Triage – update das Dashboard"
"Welche Tickets brauchen meinen Review?"
"Finde Mentions wo ich noch nicht geantwortet habe"
"Bereite Tickets für das Refinement am 2026-06-15 vor"
```

## Verfügbare Skills

| Skill                                                                  | Trigger                                     |
| ---------------------------------------------------------------------- | ------------------------------------------- |
| [find-unresponded-mentions](skills/find-unresponded-mentions/SKILL.md) | Mentions ohne Antwort von mir finden        |
| [find-stalled-tickets](skills/find-stalled-tickets/SKILL.md)           | Assigned/In-Progress Tickets ohne Aktivität |
| [find-review-needed](skills/find-review-needed/SKILL.md)               | PRs die meinen Review brauchen              |
| [ticket-triage](skills/ticket-triage/SKILL.md)                         | Alles auf einmal + Dashboard aktualisieren  |

## Scope

- Organisation: `atacama-blooms-gmbh-co-kg`
- Alle Repos der Organisation
- GitHub User: `sebastianhorwege`

## gh CLI Referenz

```bash
# Alle Repos der Org auflisten
gh repo list atacama-blooms-gmbh-co-kg --limit 100

# Issues wo ich erwähnt wurde
gh search issues --mention=@me --owner=atacama-blooms-gmbh-co-kg --state=open

# PRs die meinen Review brauchen
gh search prs --review-requested=@me --owner=atacama-blooms-gmbh-co-kg --state=open

# Meine assigned Issues
gh search issues --assignee=@me --owner=atacama-blooms-gmbh-co-kg --state=open
```
