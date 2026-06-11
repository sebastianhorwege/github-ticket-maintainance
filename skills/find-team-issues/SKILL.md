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

> ⚠️ Die Org `atacama-blooms-gmbh-co-kg` nutzt **kein** `label:blocker`. Blocker werden ausschließlich über das Priority-Feld in **Project 9** gesetzt.

**Primär – via Project 9 Priority `🌋 Blocker`:**
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

> ⚠️ `--no-assignee` ist kein gültiges `gh` CLI-Flag. Korrekte Syntax via MCP oder gh CLI Query:

```bash
# gh CLI – no:assignee im Query-String
gh search issues \
  "no:assignee org:atacama-blooms-gmbh-co-kg is:open" \
  --sort=created \
  --json number,title,repository,url,createdAt,labels
```

Alternativ via MCP:
```
mcp_github_search_issues
  query: "no:assignee is:open org:atacama-blooms-gmbh-co-kg -author:app/renovate -author:app/dependabot"
  sort: created
  per_page: 50
```

Sortierung: Älteste zuerst. Bots (renovate, dependabot) sind per Query ausgeschlossen.

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

> ⚠️ **gh CLI kann Project-Status NICHT server-seitig filtern.** Project 9 hat 2400+ Items – ein vollständiges Durchpaginieren via gh CLI dauert Minuten (50 Items/Request). Immer MCP verwenden für Status-basierte Queries.

Project 9, Bucket `🏗 In progress`, alle Items laden (mit Pagination):

```
# Seite 1
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  query: status:"🏗 In progress"
  fields: ["31475950","31475949","31475955","31475978"]
  per_page: 50

# Falls pageInfo.hasNextPage == true: Seite 2 mit after-Cursor
mcp_gh-projects_projects_list
  ...
  after: <pageInfo.nextCursor>
```

Pagination solange wiederholen bis `hasNextPage == false`. Alle Items zusammenführen.

**Auswertung per jq (nach Empfang aller Seiten):**

```bash
TODAY="YYYY-MM-DD"
jq -s --arg TODAY "$TODAY" '
(.[0].items + .[1].items) |       # alle Seiten zusammenführen
map({
  title: (.content.title // "?"),
  repo: (.content.repository // "" | split("/") | last),
  number: (.content.number // 0),
  html_url: (.content.html_url // ""),
  updated_at: (.content.updated_at // ""),
  assignees: ((.content.assignees // []) | join(",")),
  priority: ([.fields[]? | select(.id == 31475978) | .value.name] | first // ""),
  days_inactive: (
    if (.content.updated_at // "") != "" then
      ((.content.updated_at | split("T")[0] | split("-") | map(tonumber)) as $u |
      ($TODAY | split("-") | map(tonumber)) as $t |
      (($t[0]-$u[0])*365 + ($t[1]-$u[1])*30 + ($t[2]-$u[2])))
    else 999 end
  )
}) |
sort_by(.days_inactive) | reverse |
.[] |
select(.days_inactive > 21) |
[(.days_inactive|tostring), .repo, (.number|tostring), .title[:55], .assignees, .priority] | @tsv
' page1.json page2.json
```

**Was prüfen:**
- Items ohne Assignee (`.assignees == ""`) → gesondert markieren
- Items mit Priority-Feld gesetzt → in Blocker-Sektion aufnehmen
- Schwelle: > 21 Tage inaktiv = stalled

---

### 4b) Stalled Ready (nie abgeholt)

Gleiche Methode für `🔖 Ready` – Items die in Ready stehen aber nie in Arbeit genommen wurden:

```
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  query: status:"🔖 Ready"
  fields: ["31475950","31475949","31475955","31475978"]
  per_page: 50
```

Pagination wie bei In-Progress. Gleiche jq-Auswertung.

**Besondere Hinweise (Stand 2026-06-10):**
- Project 9 enthält **~81 Ready-Items**, davon **20+ über 200 Tage** nie abgeholt
- Schwelle: > 30 Tage in Ready ohne Aktivität = nie abgeholt
- Unassigned Ready-Items separat markieren
- `@sebastianhorwege` hat eigene Items in Ready: #125 (288d), #95 iso27001 (284d), #900 (253d) → Kandidaten zum Schließen oder in Backlog schieben

**ORG.md Abschnitt:** `## 🔖 Ready – nie abgeholt (Project 9)`

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
- X stalled In-Progress Tickets (> 21 Tage)
- X Ready-Items nie abgeholt (> 30 Tage)
- X Issues im Triage-Rückstand
```

**ORG.md Sektionen die befüllt werden:**
- `## 🚨 Offene Blocker`
- `## 👤 Unassigned Issues (Project 9)`
- `## 🔄 PRs ohne Review (Team)`
- `## 🐌 Stalled In-Progress (Project 9)`
- `## 🔖 Ready – nie abgeholt (Project 9)`
- `## 🏷️ Triage-Rückstand (Project 9)`
- `## 📊 Team-Auslastung`
```
