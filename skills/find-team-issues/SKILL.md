---
name: find-team-issues
description: Team-weites Monitoring für alle Repos in atacama-blooms-gmbh-co-kg. Findet Blocker, unassigned Issues, PRs ohne Review, stalled In-Progress Tickets, Triage-Rückstand und erstellt eine Team-Auslastungsübersicht. Aktualisiert den Team-Bereich in DASHBOARD.md. Use when: Team, Blocker, unassigned, Auslastung, Triage-Rückstand, kein Reviewer, Team-Übersicht, alle Tickets.
---

# Find Team Issues

Team-weites Ticket-Monitoring für `atacama-blooms-gmbh-co-kg`. Alle Repos, Project 9.

## Hardcoded Defaults

- **Owner**: `atacama-blooms-gmbh-co-kg`
- **Project Number**: `9`
- **Field-IDs** (Project 9):
  - Status: `31475950`
  - Assignees: `31475949`
  - Repository: `31475955`
  - Priority: `31475978`
- **Stall-Schwelle**: 21 Tage ohne Aktivität

## Quick start

Alle Sektionen sequenziell befüllen, dann Dashboard aktualisieren. Keine Rückfragen nötig.

---

## Workflow

### 1) Offene Blocker finden

```bash
gh search issues \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --label=blocker \
  --sort=updated \
  --json number,title,repository,url,assignees,updatedAt
```

Zusätzlich via Project 9 nach Priorität `🌋 Blocker` suchen:
```
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  query: priority:"🌋 Blocker"
  fields: ["31475950","31475949","31475955","31475978"]
  per_page: 50
```

Inaktiv-Tage berechnen: `heute - updatedAt`.
Items mit `inaktiv > 7 Tage` zusätzlich in `🔥 Sofort handeln` aufnehmen.

---

### 2) Unassigned Issues finden

```bash
gh search issues \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --no-assignee \
  --sort=created \
  --json number,title,repository,url,createdAt,labels
```

Sortierung: Älteste zuerst. Bots (renovate, dependabot) herausfiltern.

---

### 3) PRs ohne Review finden

```bash
gh search prs \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --sort=updated \
  --json number,title,repository,url,author,createdAt,isDraft,reviewDecision
```

Filter: `reviewDecision` ist `null` oder `REVIEW_REQUIRED` (kein Review abgegeben).
Draft-PRs (`isDraft: true`) separat kennzeichnen, nicht herausfiltern.

---

### 4) Stalled In-Progress (Team)

Project 9, Bucket `🏗 In progress`, alle Items laden:

```
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  query: status:"🏗 In progress"
  fields: ["31475950","31475949","31475955","31475978"]
  per_page: 50
```

Pagination via `pageInfo.nextCursor` durchlaufen.

Filter: `updatedAt < heute - 21 Tage`.
Items mit Assignees=0 mit ⚠️ markieren.

---

### 5) Triage-Rückstand

Issues ohne Priorität im Project Board:
```
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  fields: ["31475950","31475978"]
  per_page: 50
```

Filter: `priority == null` oder Priority-Feld leer.
Alternativ via gh CLI:
```bash
gh search issues \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --no-milestone \
  --sort=created \
  --json number,title,repository,url,createdAt,labels,milestone
```

Zeige nur Issues ohne Milestone UND ohne Priority-Label.

---

### 6) Team-Auslastung berechnen

Alle assigned Issues + PRs pro Person aggregieren:

```bash
gh search issues \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --sort=updated \
  --json assignees,repository,url
```

Dann nach `assignees[].login` gruppieren und zählen.
Zusätzlich aus Project 9 Status-Breakdown pro Assignee:

```
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  query: status:"🏗 In progress"
  fields: ["31475949","31475950"]
  per_page: 100
```

Ergebnis-Tabelle:

```markdown
| Mitglied | In Progress | Todo | Gesamt |
|----------|-------------|------|--------|
| @username | 3 | 5 | 8 |
```

Sortierung: Gesamt absteigend (höchste Auslastung oben).

---

## Dashboard aktualisieren

Ersetze alle Tabellen im Abschnitt `# 🏢 Team-Übersicht` in `DASHBOARD.md`.
Watchlist (`📋 Watchlist`) **nie** überschreiben.

### Sofort handeln – Team-Items ergänzen

Füge zu `## 🔥 Sofort handeln` hinzu (ohne bestehende persönliche Items zu löschen):
- Blocker inaktiv > 7 Tage
- Unassigned Blocker
- PRs offen > 5 Tage ohne jeglichen Review

## Output-Format

Nach Abschluss kompakte Zusammenfassung:
```
Team-Übersicht:
- X Blocker offen (Y davon inaktiv > 7 Tage)
- X unassigned Issues
- X PRs ohne Review
- X stalled In-Progress Tickets
- X Issues im Triage-Rückstand
```
