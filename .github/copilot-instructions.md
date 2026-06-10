---
applyTo: "**"
---

# GitHub Ticket Maintainance – Copilot Kontext

Dieses Repo dient dem Monitoring und der Verwaltung von GitHub-Tickets in der Organisation **atacama-blooms-gmbh-co-kg**.

## Grundkontext

- **GitHub User**: `sebastianhorwege`
- **Organisation**: `atacama-blooms-gmbh-co-kg` (alle Repos)
- **Persönliches Dashboard**: `DASHBOARD.md` – Mentions, Reviews, Assigned, Watchlist, Termine
- **Org-Übersicht**: `ORG.md` – Team-Übersicht, Zombie PRs, Org-weite Altlasten
- **Tool-Präferenz**: GitHub MCP (`mcp_github_*`, `mcp_gh-projects_*`) + `gh` CLI

## Verhalten

- Persönliche Daten → `DASHBOARD.md` · Team/Org-Daten → `ORG.md`
- Beim Update den Timestamp `> Zuletzt aktualisiert:` in der jeweiligen Datei anpassen (Datum + Uhrzeit)
- Tabellen vollständig ersetzen, nicht nur Zeilen anhängen
- Termine in `DASHBOARD.md ## 📅 Termine` als eigene `### 📆 ...` Unterabschnitte anlegen
- Neue Termine nicht überschreiben, sondern darunter einfügen (nach Datum sortiert)
- Bei manueller Watchlist (`## 📋 Watchlist` in `DASHBOARD.md`) niemals Einträge löschen
- Zombie-PR-Daten (org-weit) → `ORG.md # 🧟 Zombie PRs`-Abschnitt

## Verfügbare Skills (in diesem Repo)

Lade diese Skills wenn der User entsprechende Aktionen anfragt:

| Skill | Pfad | Wann laden |
|-------|------|-----------|
| find-unresponded-mentions | `skills/find-unresponded-mentions/SKILL.md` | "Mentions", "geantwortet", "erwähnt" |
| find-stalled-tickets | `skills/find-stalled-tickets/SKILL.md` | "stalled", "keine Aktivität", "hängen", "assigned" |
| find-review-needed | `skills/find-review-needed/SKILL.md` | "Review", "PR", "prüfen" |
| find-team-issues | `skills/find-team-issues/SKILL.md` | "Team", "Blocker", "unassigned", "Auslastung", "Triage-Rückstand", "Team-Übersicht" |
| find-org-stale | `skills/find-org-stale/SKILL.md` | "Zombie PRs", "stale PRs", "Altlasten", "alte PRs", "org-weit", "nicht aktualisiert" |
| ticket-triage | `skills/ticket-triage/SKILL.md` | "Triage", "Dashboard aktualisieren", "alles" |
| handle-ticket | `skills/handle-ticket/SKILL.md` | "bearbeite #X", "nächstes Item", "draft Antwort", "ping an", "zuweisen" |

## Team-Mitglieder (atacama-blooms-gmbh-co-kg)

> Quelle: GitHub Team `blooms`. Namen-Mapping aus Mitarbeiterverzeichnis.
> GitHub-Usernames werden in neuem Doku-Release in Profile aufgenommen.

| GitHub Username | Name | Rolle |
|----------------|------|-------|
| `sebastianhorwege` | Sebastian Horwege | Leitung Softwareentwicklung |
| `Zebbities` | Sebastian Zebbities | Geschäftsführer |
| `jasper-ooster` | Jasper Ooster | Leitung AI Solutions |
| `carinawagner-atacama` | Carina Wagner | Architektin |
| `ablPatDel` | Patrick Delecate | Product Owner / Projektleitung |
| `SebastianHalfter` | Sebastian Halfter | Software Engineer (Loom) |
| `AuchterSimon` | Simon Scharf | DevOps / Infrastruktur |
| `MarcelJordanblooms` | Marcel Jordan | Leitung Datenerfassung |
| `antonschlegel` | Anton Schlegel | Software Engineer (Backend) |
| `Marius-Beuerle` | Marius Beuerle | KI-Entwickler |
| `BeckerAdrian` | Adrian Becker | KI-Entwickler |
| `Tarakzai` | Ali Tarakzai | KI-Entwickler |
| `hironmoyahmadanan` | Hironmoy Ahmad | KI-Entwickler |
| `TjarkNieber` | Tjark Nieber | KI-Entwickler |
| `Rohanrajan017` | Rohana Rajan | KI-Entwickler |
| `Olivia-Nguimdo` | Olivia Nguimdo | KI-Entwicklerin |
| `msuhail2003` | Muhammad Suhail | — |
| `danladis` | Daniel Ladischenski | — |
| `YveGo` | Yvonne Goltsche | — |
| `yannikHan` | Yannik Hangst | — |
| `LornaScharf` | Lorna Scharf | — |
| `mariem-baratli` | Mariam Baratli | — |
| `phil-degrandi` | Phil De Grandi | — |
| `jan-korte` | Jan Korte | — |
| `isisripke` | Isis Ripke | — |
| `FlemmDa` | — | — | 
| `Atacama-KV-Diana` | Diana Köhler | Projektmanagerin (KV) |
| `CMaletz` | Christine Maletz | Bereichsleitung PM (KV) |

> Accounts die beim Auslastungs-Check ignoriert werden: `atacamablooms` (Org-Account)

## gh CLI Basis-Queries

```bash
# Mentions
gh search issues --mention=@me --owner=atacama-blooms-gmbh-co-kg --state=open --sort=updated

# Review Requests
gh search prs --review-requested=@me --owner=atacama-blooms-gmbh-co-kg --state=open --sort=updated

# Assigned Issues
gh search issues --assignee=@me --owner=atacama-blooms-gmbh-co-kg --state=open --sort=updated

# Assigned PRs
gh search prs --assignee=@me --owner=atacama-blooms-gmbh-co-kg --state=open --sort=updated
```
