---
name: find-review-needed
description: Findet PRs in atacama-blooms-gmbh-co-kg die einen Review von msebhorw angefragt haben. Aktualisiert DASHBOARD.md Sektion "PRs warten auf meinen Review". Use when: Review, PR prüfen, review-requested, Code Review.
---

# Find Review Needed

Findet alle offenen PRs in `atacama-blooms-gmbh-co-kg` bei denen ein Review von `msebhorw` angefragt wurde.

## Quick start

1. Review-Requests laden
2. Nach Alter sortieren (älteste zuerst)
3. `DASHBOARD.md` Sektion `👀 PRs warten auf meinen Review` aktualisieren

## Workflow

### Schritt 1 – Review Requests laden

```bash
gh search prs \
  --review-requested=@me \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --sort=updated \
  --json number,title,repository,url,author,createdAt,updatedAt,isDraft,additions,deletions
```

Via MCP:
```
mcp_github_search_pull_requests
  query: "review-requested:msebhorw is:pr is:open org:atacama-blooms-gmbh-co-kg"
  per_page: 50
```

### Schritt 2 – Draft PRs herausfiltern

Draft-PRs (`isDraft: true`) separat markieren oder weglassen (User-Präferenz, Standard: anzeigen aber als Draft kennzeichnen).

### Schritt 3 – Priorisierung

| Priorität | Kriterium |
|-----------|-----------|
| 🔴 Hoch | Review-Request älter als 3 Tage |
| 🟡 Mittel | Review-Request 1–3 Tage alt |
| 🟢 Neu | Review-Request heute/gestern |

Alter = `heute - createdAt` (des Review-Requests, nicht des PRs).
Da Review-Request-Datum nicht direkt verfügbar: `updatedAt` als Näherung nutzen.

### Schritt 4 – Dashboard aktualisieren

Ersetze Tabelle unter `## 👀 PRs warten auf meinen Review` in `DASHBOARD.md`:

```markdown
| PR | Repo | Titel | Autor | Alter (Tage) | Link |
|----|------|-------|-------|--------------|------|
| 789 | repo-name | Fix: something important | @kollege | 4 🔴 | [#789](url) |
```

### Schritt 5 – Sofort handeln

PRs mit Alter > 3 Tage zusätzlich in `## 🔥 Sofort handeln` aufnehmen mit Grund "Review überfällig".

## Output-Format

Tabelle zeigen + Gesamtzahl ("X PRs warten auf deinen Review").
Bei > 5 PRs: fragen ob direkt einen öffnen.
