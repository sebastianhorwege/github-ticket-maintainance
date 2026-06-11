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
  "updated:<$CUTOFF archived:false" \
  --json number,repository,title,author,updatedAt,createdAt,isDraft \
  --limit 200

# Kritisch (> 180 Tage)
CUTOFF=$(date -d "180 days ago" +%Y-%m-%d)
gh search prs \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  "updated:<$CUTOFF archived:false" \
  --json number,repository,title,author,updatedAt,createdAt,isDraft \
  --limit 200

# Zombie (> 365 Tage / 1 Jahr)
CUTOFF=$(date -d "365 days ago" +%Y-%m-%d)
gh search prs \
  --owner=atacama-blooms-gmbh-co-kg \
  --state=open \
  "updated:<$CUTOFF archived:false" \
  --json number,repository,title,author,updatedAt,createdAt,isDraft \
  --limit 200
```

## MCP Alternative (mcp_github_search_pull_requests)

```
mcp_github_search_pull_requests:
  query: "is:pr is:open org:atacama-blooms-gmbh-co-kg updated:<{CUTOFF} archived:false"
  sort: "updated"
  order: "asc"
  per_page: 100
```

---

## Workflow

### Schritt 0: Archivierte Repos NIEMALS betrachten

> ⚠️ **Wichtig:** Der GitHub-Search-Qualifier `archived:false` ist **unzuverlässig** (gecacht / greift nicht immer). Archivierte Repos MÜSSEN zusätzlich hart herausgefiltert werden.

**Bekannte archivierte Repos (Stand 2026-06-11) – immer ausschließen:**
`ansible`, `avidoc-r-doctagger`, `chat-with-blooms-docu`, `exploreblooms-backend`, `human-ocr`, `kidohe`, `loom-62-digital-backend`, `model-publishing-object-detection-fields-hkp-verord`, `nks_conversation_prototype`, `open-webui-blooms`

> ⚠️ Diese Liste ist nicht abschließend – **immer** zusätzlich zur Laufzeit verifizieren (Search liefert auch archivierte Repos, die nicht in der Liste stehen, z.B. `kidohe` tauchte nur bei `--sort updated --order asc` auf).

**Verifikation zur Laufzeit** – für jedes Repo das in den Suchergebnissen auftaucht den Archiv-Status prüfen und archivierte rauswerfen:

```bash
# Archiv-Status eines Repos pruefen (true = archiviert -> ausschliessen)
gh api repos/atacama-blooms-gmbh-co-kg/<repo> -q '.archived'

# Alle in den Ergebnissen vorkommenden Repos auf einmal pruefen:
for r in $(cat /tmp/zombie_repos.txt | sort -u); do
  a=$(gh api repos/atacama-blooms-gmbh-co-kg/$r -q '.archived' 2>/dev/null)
  [ "$a" = "true" ] && echo "$r"  # diese Repos komplett ignorieren
done
```

Archivierte Repos werden **nicht** in den Tabellen gelistet (auch nicht durchgestrichen) und zählen **nicht** in die PR-Gesamtzahlen.

### Schritt 1: Daten abrufen

Schneide alle 3 Stufen ab:
- `CUTOFF_90 = heute - 90 Tage`
- `CUTOFF_180 = heute - 180 Tage`
- `CUTOFF_365 = heute - 365 Tage`

Führe Zombie-Query (> 365 Tage) als Hauptquery aus. Kritisch- und Warnungs-Counts können additiv berechnet werden.

### Schritt 2: Nach Repo gruppieren

Erstelle `repo → [prs]` Map. **Entferne zuerst alle archivierten Repos** (siehe Schritt 0 – Ausschlussliste + Laufzeit-Verifikation über ALLE vorkommenden Repos, nicht nur die bekannten). Sortiere die verbleibenden Repos nach Anzahl PRs (absteigend).

> 💡 Tipp: Suche **zweimal** ausführen – einmal Default (best-match) und einmal `--sort updated --order asc` – und die Ergebnisse vereinen + nach `repo#number` deduplizieren. Sonst fehlen die ganz alten PRs (Search-Limit 300, default-Sortierung schneidet die ältesten ab).

**Bekannte Problemrepos (aktiv, Stand 2026-06-11):**
- `avidoc-r` – mehrere alte Bugfix-PRs (Haupt-Problemrepo)
- `k8s-config-avidoc-r`, `avidoc-r-api`, `flower`, `blooms-utils` – diverse Altlasten

**Archivierte Repos (immer herausfiltern, NIE listen):**
`ansible`, `kidohe`, `avidoc-r-doctagger`, `human-ocr`

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
- **`archived:false` im Search-Query ist unzuverlässig** – archivierte Repos zusätzlich per `gh api repos/<org>/<repo> -q .archived` verifizieren und hart rausfiltern (siehe Schritt 0)
- Archiviert (immer ignorieren): `ansible`, `kidohe`, `avidoc-r-doctagger`, `human-ocr`
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
