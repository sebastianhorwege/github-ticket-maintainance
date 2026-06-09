---
name: find-unresponded-mentions
description: Findet Issues und PRs in der Org atacama-blooms-gmbh-co-kg wo sebastianhorwege erwähnt wurde aber noch nicht geantwortet hat. Aktualisiert DASHBOARD.md Sektion "Mentions ohne Antwort". Use when: Mentions, geantwortet, erwähnt, @me, unanswered.
---

# Find Unresponded Mentions

Findet alle Issues/PRs in `atacama-blooms-gmbh-co-kg` wo `sebastianhorwege` @mentioned wurde, aber **selbst noch keinen Kommentar** hinterlassen hat.

## Quick start

1. Mentions suchen
2. Pro Treffer: letzten Kommentar-Autor prüfen
3. Wenn kein Kommentar von `sebastianhorwege` → in Liste aufnehmen
4. `DASHBOARD.md` Sektion `📥 Mentions ohne Antwort` aktualisieren

## Workflow

### Schritt 1 – Mentions laden

```bash
gh search issues \
  --mention=@me \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  --sort=updated \
  --json number,title,repository,url,updatedAt,comments
```

Alternativ via MCP:
```
mcp_github_search_issues
  query: "mentions:sebastianhorwege is:open org:atacama-blooms-gmbh-co-kg"
  per_page: 50
```

### Schritt 2 – Eigene Antwort prüfen

Pro Issue/PR: Kommentare laden und prüfen ob `sebastianhorwege` einer der Autoren ist.

```bash
gh api repos/{owner}/{repo}/issues/{number}/comments \
  --jq '[.[] | {author: .user.login, created: .created_at}]'
```

Via MCP:
```
mcp_github_issue_read
  owner: <repo-owner>
  repo: <repo-name>
  issue_number: <number>
```
→ Prüfe `comments[].user.login` auf `sebastianhorwege`

### Schritt 3 – Filtern

Behalte nur Items wo **kein** Kommentar von `sebastianhorwege` existiert.

Priorisierung:
- Älteste `updatedAt` zuerst (am längsten wartend)
- PRs vor Issues (blockieren oft andere)

### Schritt 4 – Dashboard aktualisieren

Ersetze die Tabelle unter `## 📥 Mentions ohne Antwort` in `DASHBOARD.md`:

```markdown
| # | Repo | Titel | Geöffnet | Letzter Kommentar | Link |
|---|------|-------|----------|-------------------|------|
| 123 | repo-name | Titel des Issues | 2026-05-01 | @username (2026-06-01) | [#123](url) |
```

## Output-Format

Zeige dem User die Tabelle und frage ob eine Antwort direkt geöffnet/vorbereitet werden soll.
