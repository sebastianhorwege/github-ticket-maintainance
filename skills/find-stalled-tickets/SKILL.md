---
name: find-stalled-tickets
description: Findet Tickets (Issues/PRs) in atacama-blooms-gmbh-co-kg die sebastianhorwege assigned sind aber seit > 14 Tagen keine Aktivität haben. Aktualisiert DASHBOARD.md Sektion "Assigned – keine Aktivität". Use when: stalled, keine Aktivität, hängen, assigned, nicht vorankommen.
---

# Find Stalled Tickets

Findet alle `sebastianhorwege` zugewiesenen Issues und PRs in `atacama-blooms-gmbh-co-kg` ohne nennenswerte Aktivität.

## Quick start

1. Assigned Issues + PRs laden
2. Letzte Aktivität pro Item bestimmen
3. Items mit > 14 Tagen Inaktivität filtern
4. `DASHBOARD.md` Sektion `🏗 Assigned – keine Aktivität` aktualisieren

## Inaktivitäts-Schwellen

| Schwelle | Default | Bedeutung |
|----------|---------|-----------|
| `inaktiv_tage` | 14 | Tage seit letzter Aktivität (Kommentar, Label, Status) |
| `alter_tage` | 90 | Tickets älter als X Tage besonders markieren |

User kann Schwellen anpassen.

## Workflow

### Schritt 1 – Assigned Issues laden

```bash
gh search issues \
  --assignee=@me \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --sort=updated \
  --json number,title,repository,url,updatedAt,createdAt,labels
```

### Schritt 2 – Assigned PRs laden

```bash
gh search prs \
  --assignee=@me \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --sort=updated \
  --json number,title,repository,url,updatedAt,createdAt,isDraft
```

### Schritt 3 – Filtern nach Inaktivität

```
inaktiv_seit = heute - updatedAt
behalte wenn inaktiv_seit > 14 Tage
```

### Schritt 4 – Anreicherung

Pro Item: kurz prüfen ob es im GitHub Project Board ist:
```
mcp_gh-projects_projects_list
  method: list_project_items
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  query: <issue/pr url>
```

### Schritt 5 – Dashboard aktualisieren

Ersetze Tabelle unter `## 🏗 Assigned – keine Aktivität` in `DASHBOARD.md`:

```markdown
| # | Repo | Titel | Letzte Aktivität | Tage inaktiv | Link |
|---|------|-------|-----------------|--------------|------|
| 456 | repo-name | Titel | 2026-05-20 | 20 | [#456](url) |
```

Markiere Items mit > 90 Tagen mit ⚠️.

## Output-Format

Tabelle zeigen, danach fragen:
- Soll eines der Tickets ins "Sofort handeln" aufgenommen werden?
- Soll ein Ticket für einen Termin vorbereitet werden?
