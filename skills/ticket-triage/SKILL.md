---
name: ticket-triage
description: Vollständige Triage aller GitHub-Tickets für sebastianhorwege in atacama-blooms-gmbh-co-kg. Führt alle Find-Skills aus und aktualisiert DASHBOARD.md (persönlich) + ORG.md (Team/Org) komplett. Use when: Triage, Dashboard aktualisieren, alles auf einmal, was liegt an, Überblick.
---

# Ticket Triage

Vollständiger Triage-Durchlauf: alle Find-Skills sequenziell ausführen und beide Dashboard-Dateien aktualisieren.
- **Persönlich** → `DASHBOARD.md`
- **Team / Org-weit** → `ORG.md`

## Quick start

```
"Mach ein Triage"
"Update das Dashboard"
"Was liegt alles an?"
```

→ Direkt starten, keine Rückfragen nötig.

## Reihenfolge

**Persönlich:**
1. **Mentions ohne Antwort** → `skills/find-unresponded-mentions/SKILL.md`
2. **PRs auf Review** → `skills/find-review-needed/SKILL.md`
3. **Stalled Assigned Tickets** → `skills/find-stalled-tickets/SKILL.md`

**Team:**
4. **Team-Issues** → `skills/find-team-issues/SKILL.md` (Blocker, Unassigned, PRs ohne Review, Stalled Team, Triage-Rückstand, Auslastung) → Ergebnis in `ORG.md`
5. **Zombie PRs** → `skills/find-org-stale/SKILL.md` (org-weite stale PRs) → Ergebnis in `ORG.md # 🧟 Zombie PRs`

6. **Dashboards zusammenführen** (dieser Skill)

## Dashboards zusammenführen

Nach Abschluss aller Find-Skills:

### DASHBOARD.md – persönliche Sektionen aktualisieren

Sammle aus allen Skills die dringenden Items und trage sie in `## 🔥 Sofort handeln` ein:

| Kriterium | Aus Skill |
|-----------|----------|
| Mention > 3 Tage alt ohne Antwort | find-unresponded-mentions |
| Review-Request > 3 Tage alt | find-review-needed |
| Assigned Ticket inaktiv > 30 Tage | find-stalled-tickets |
| Assigned Blocker-Ticket inaktiv > 7 Tage | find-stalled-tickets |
| Team-Blocker inaktiv > 7 Tage | find-team-issues |
| Unassigned Blocker | find-team-issues |
| PR offen > 5 Tage ohne Review | find-team-issues |

### Timestamp setzen (DASHBOARD.md)

Ersetze in `DASHBOARD.md`:
```
> Zuletzt aktualisiert: _noch nicht befüllt – führe Triage aus_
```
mit:
```
> Zuletzt aktualisiert: YYYY-MM-DD HH:MM Uhr
```

### ORG.md – Team-Sektionen aktualisieren

Ergebnisse aus `find-team-issues` in `ORG.md` eintragen:
- `## 🚨 Offene Blocker`
- `## 👤 Unassigned Issues`
- `## 🔄 PRs ohne Review (Team)`
- `## 🐌 Stalled In-Progress`
- `## 🏷️ Triage-Rückstand`
- `## 📊 Team-Auslastung`

Ergebnisse aus `find-org-stale` in `ORG.md # 🧟 Zombie PRs – Org-weit` eintragen.

### Timestamp setzen (ORG.md)

Ersetze in `ORG.md`:
```
> Zuletzt aktualisiert: ...
```
mit aktuellem Datum + Uhrzeit.

## Termin-Vorbereitung (optional)

Wenn der User einen Termin nennt, **immer zuerst den Termin-Typ abfragen**:

```
"Bereite Tickets für [Termin] am [Datum] vor"
```

### Schritt 1 – Termin-Typ bestimmen

Frage: "Was für ein Termin ist das?"

| Typ | Trigger-Wörter |
|-----|---------------|
| Refinement | "Refinement", "Grooming", "Backlog-Review" |
| Sprint Review | "Review", "Sprint Review", "Demo" |
| 1:1 | "1:1", "Eins-zu-eins", + Name einer Person |
| Standup / Jour Fixe | "Standup", "Jour Fixe", "Daily", "Weekly" |
| Sonstiges | alles andere |

Wenn der Termin-Typ aus dem Namen eindeutig hervorgeht (z.B. "Refinement"), direkt fortfahren ohne Rückfrage.

### Schritt 2 – Tickets nach Typ laden

**Refinement:**
```
Project 9 Status: "📋 Backlog" + "✅ Ready"
Ziel: Tickets die noch nicht geschätzt/triagiert sind
```

**Sprint Review:**
```
Project 9 Status: "🏗 In progress" + "🧪 Testing" (oder ähnliche Testing-Spalten)
Ziel: Was wurde fertig, was ist noch offen?
```

**1:1 mit Person X:**
```
Project 9: alle Items mit Assignee = <GitHub-Username von Person X>
Status: "🏗 In progress" + "✅ Ready"
Ziel: Womit ist die Person gerade beschäftigt / blockiert?
```

**Standup / Jour Fixe:**
```
Project 9 Status: "🏗 In progress" + "🧪 Testing"
Einschränkung: nur Items mit Assignee in der Teilnehmer-Liste (falls bekannt)
```

**Sonstiges:**
```
Kurz fragen: "Welche Tickets sollen auf die Agenda? Soll ich Ready + In Progress laden?"
```

### Schritt 3 – Kandidaten zeigen und auswählen

Liste die gefundenen Items auf und lass den User auswählen welche auf die Agenda kommen:
```
Gefundene Items (X Stück) – welche sollen auf die Agenda?
[nummerierte Liste]
Tippe die Nummern oder "alle"
```

### Schritt 4 – Dashboard-Sektion anlegen

Unter `## 📅 Termine` neuen Unterabschnitt einfügen (nach Datum sortiert, bestehende nicht löschen):

```markdown
### 📆 [Termin-Titel] – [Datum]

**Typ:** [Refinement / Sprint Review / 1:1 / ...]

| # | Repo | Titel | Was vorbereiten | Link |
|---|------|-------|-----------------|------|
| 123 | repo | Titel | Diskussionspunkte klären | [#123](url) |
```

## Abschluss

Nach vollständigem Update:
- Kurze Zusammenfassung ausgeben:
  ```
  Persönlich: X Mentions, Y Reviews, Z stalled
  Team: A Blocker, B unassigned, C PRs ohne Review, D stalled, E Triage-Rückstand
  ```
- Fragen ob ein Termin vorbereitet werden soll
