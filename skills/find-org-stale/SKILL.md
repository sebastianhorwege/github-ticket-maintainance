---
name: find-org-stale
description: Org-weite Suche nach veralteten PRs (Zombie PRs) in atacama-blooms-gmbh-co-kg. Gruppiert nach Repo, zeigt Worst Offender und befüllt ORG.md Abschnitt "Zombie PRs".
---

# Skill: find-org-stale

## Zweck

Findet alle offenen PRs in der Org `atacama-blooms-gmbh-co-kg` die seit > 90 Tagen keine Aktivität hatten.
Liefert eine nach Repo gruppierte Übersicht + sortierte Liste der ältesten PRs.
Ergebnis wird in `ORG.md` im Abschnitt `# 🧟 Zombie PRs – Org-weit` gespeichert.

---

## Trigger-Phrasen

- "find-org-stale"
- "Zombie PRs"
- "stale PRs org-weit"
- "Org-weite Altlasten"
- "alte PRs über alle Repos"

---

## Stufenmodell

| Stufe | Grenze | Symbol | Bedeutung |
|-------|--------|--------|-----------|
| Warnung | > 90 Tage | 🟡 | Bewegungslos aber noch frisch genug zum Reviewen |
| Kritisch | > 180 Tage | 🟠 | Wahrscheinlich vergessen – aktiv nachfragen |
| Zombie | > 365 Tage | 🔴 | Schließen oder explizit reaktivieren |

---

## gh CLI Queries

```bash
# Alle stale PRs (nicht aktualisiert seit > 90 Tage)
CUTOFF=$(date -d "90 days ago" +%Y-%m-%d)
gh search prs \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  "updated:<$CUTOFF" \
  --json number,repository,title,author,updatedAt,createdAt,isDraft \
  --limit 200

# Kritisch (> 180 Tage)
CUTOFF=$(date -d "180 days ago" +%Y-%m-%d)
gh search prs \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  "updated:<$CUTOFF" \
  --json number,repository,title,author,updatedAt,createdAt,isDraft \
  --limit 200

# Zombie (> 365 Tage / 1 Jahr)
CUTOFF=$(date -d "365 days ago" +%Y-%m-%d)
gh search prs \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  "updated:<$CUTOFF" \
  --json number,repository,title,author,updatedAt,createdAt,isDraft \
  --limit 200
```

## MCP Alternative (mcp_github_search_pull_requests)

```
mcp_github_search_pull_requests:
  query: "is:pr is:open org:atacama-blooms-gmbh-co-kg updated:<{CUTOFF}"
  sort: "updated"
  order: "asc"
  per_page: 100
```

---

## Workflow

### Schritt 1: Daten abrufen

Schneide alle 3 Stufen ab:
- `CUTOFF_90 = heute - 90 Tage`
- `CUTOFF_180 = heute - 180 Tage`
- `CUTOFF_365 = heute - 365 Tage`

Führe Zombie-Query (> 365 Tage) als Hauptquery aus. Kritisch- und Warnungs-Counts können additiv berechnet werden.

### Schritt 2: Nach Repo gruppieren

Erstelle `repo → [prs]` Map. Sortiere Repos nach Anzahl PRs (absteigend).

**Bekannte Problemrepos (Stand 2026-06-10):**
- `ansible` – 14+ alte PRs, viele vom User `openglfreak` und `sebastianhorwege` (Altlasten aus Infra-Zeiten)
- `avidoc-r-doctagger` – 2 sehr alte PRs (1800+ Tage), Repo scheint inaktiv
- `kidohe` – 2 alte WIP-PRs (beide 1600+ Tage)
- `avidoc-r` – mehrere alte Bugfix-PRs

### Schritt 3: Top 20 Liste erstellen

Sortiere alle PRs nach `updatedAt` aufsteigend (älteste zuerst).
Format:

```
| #   | Repo         | Titel (gekürzt auf 45 Zeichen) | Autor | Alter (Tage) | Draft | Link |
```

Alter = `(heute - updatedAt).days`

Draft-Symbol: `🟡` wenn `isDraft = true`, sonst `🔴`

### Schritt 4: ORG.md aktualisieren

Ersetze in `ORG.md` den Abschnitt `# 🧟 Zombie PRs – Org-weit` komplett (von `# 🧟` bis Ende Datei oder nächstes `---`).

Tabellen vollständig ersetzen – nicht anhängen.
Timestamp in `> Zuletzt aktualisiert:` im Zombie-Abschnitt anpassen.

### Schritt 5: Empfehlung ausgeben

Schreibe kurze Empfehlung für die ältesten nicht-Draft PRs:
- PR älter 365 Tage + kein Review + kein Draft → Empfehlung: **Schließen oder Kommentar**: _"Ist dieser PR noch relevant?"_
- PR ist Draft + kein Update seit 180 Tage → Empfehlung: **Draft schließen**
- PR hat Review-Request an `sebastianhorwege` → in DASHBOARD.md unter „PRs warten auf meinen Review" schon erfasst

---

## Hinweise & Bekannte Constraints

- `label:blocker` existiert **nicht** in der Org – nicht verwenden
- `--no-assignee` ist kein valides `gh`-Flag – stattdessen `no:assignee` im Query-String
- ansible-Repo hat viele DRAFT-PRs von `sebastianhorwege` aus alten Zeiten – wurden nie gemergt/geschlossen
- `openglfreak` ist ein ehemaliger Mitarbeiter (Account nicht mehr aktiv) – dessen PRs sind Kandidaten zum Schließen
- `H-CLindner` ist ebenfalls nicht mehr aktiv
- PR-Counts variieren da GitHub-Search gecacht ist (± 10-20 PRs je nach Zeitpunkt)

---

## Ausgabe-Vorlage (für ORG.md)

```markdown
# 🧟 Zombie PRs – Org-weit

> Alle offenen PRs in `atacama-blooms-gmbh-co-kg` die seit > 90 Tagen keine Aktivität haben.
> Aktualisiert via: `find-org-stale` Skill

> Zuletzt aktualisiert: {DATUM}
> **{N90} PRs** nicht aktualisiert seit > 90 Tagen · **{N365} PRs** älter als 1 Jahr

---

## 📦 Betroffene Repos

| Repo | Stale PRs | Ältester | Hinweis |
|------|-----------|----------|---------|
| ... |

---

## 🏆 Top 20: Älteste PRs

| PR | Repo | Titel | Autor | Alter (Tage) | Draft | Link |
|----|------|-------|-------|--------------|-------|------|
| ... |
```
