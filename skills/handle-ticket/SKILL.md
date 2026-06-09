---
name: handle-ticket
description: Bearbeitet ein einzelnes Ticket iterativ: analysiert welche Aktionen sinnvoll sind (GitHub-Kommentar, Assign, Teams-Ping), draftet alles auf einmal und fragt nach dem nächsten Item. Use when: "bearbeite #X", "bearbeite dieses Ticket", "nächstes Item", "draft Antwort", "ping an", "zuweisen".
---

# Handle Ticket

Bereitet alle sinnvollen Aktionen für ein einzelnes Ticket vor: GitHub-Kommentar-Entwurf, Zuweisung, Teams-Nachricht. Iterativer Workflow – nach jeder Bearbeitung zum nächsten Item anbieten.

## Quick start

```
"bearbeite #123"
"bearbeite Issue #456 in repo-name"
"nächstes Item"
```

→ Ticket-Kontext laden, Aktionstyp bestimmen, alles in einem Zug vorbereiten.

---

## Schritt 1 – Ticket-Kontext laden

```
mcp_github_issue_read
  owner: <org/repo-owner>
  repo: <repo-name>
  issue_number: <number>
```

Lade: Titel, Body, Labels, Assignees, letzter Kommentar, Status im Project 9.

---

## Schritt 2 – Aktionstyp bestimmen

Anhand des Kontexts die passenden Aktionen auswählen:

| Situation | Aktion |
|-----------|--------|
| Mention ohne Antwort | GitHub-Kommentar draften |
| Blocker ohne Assignee | Assignee vorschlagen + Teams-Ping an Lead |
| Stalled In-Progress | Teams-Ping an aktuellen Assignee |
| Review-Request > 3 Tage | GitHub-Review-Kommentar draften ODER Teams-Ping an `msebhorw` |
| Triage-Rückstand (keine Prio) | Priorität + Assignee im Project 9 vorschlagen |
| PR ohne Review (Team) | Teams-Ping an zuständigen Reviewer |

Mehrere Aktionen können kombiniert werden.

---

## Schritt 3 – GitHub-Kommentar draften (wenn relevant)

Entwurf basierend auf Ticket-Kontext:

- Bei Mention: kurze, direkte Antwort auf die Frage/das Anliegen
- Bei Blocker: Status-Update anfordern oder Hilfe anbieten
- Bei Review: konkrete Review-Anmerkungen oder Freigabe

Entwurf dem User zur Bestätigung zeigen **bevor** er gepostet wird.

```
mcp_github_add_issue_comment   ← erst nach Bestätigung
  owner: <owner>
  repo: <repo>
  issue_number: <number>
  body: <entwurf>
```

---

## Schritt 4 – Teams-Nachricht draften (wenn relevant)

Bestimme die Zielperson:
- Ticket-Autor → aus Issue `user.login` → Name aus Team-Mitglieder-Tabelle in `copilot-instructions.md` nachschlagen
- Assignee → aus Issue `assignees[].login`
- Kein Assignee + Blocker → an `msebhorw` (Leitung) oder passendes Team-Lead

Teams-Deeplink via `communication-deeplinks` Skill:
```
Lade: skills/../communication-deeplinks  (falls verfügbar)
Oder: manuellen Teams-Chat-Link bauen:
https://teams.microsoft.com/l/chat/0/0?users=<email>
```

Name → E-Mail Mapping: aus Atacama Blooms Doku-Portal nachschlagen wenn nicht bekannt.

Entwurf der Teams-Nachricht zeigen (nicht automatisch senden).

---

## Schritt 5 – Assign / Label setzen (wenn relevant)

Bei unassigned Blocker: Zuweisung vorschlagen und nach Bestätigung setzen:
```
mcp_github_issue_write
  owner: <owner>
  repo: <repo>
  issue_number: <number>
  assignees: [<username>]
```

Bei fehlendem Priority-Label im Project 9:
```
mcp_gh-projects_projects_write
  method: update_project_item_field
  owner: atacama-blooms-gmbh-co-kg
  owner_type: org
  project_number: 9
  field_id: "31475978"   ← Priority
  value: <vorgeschlagene Priorität>
```

---

## Schritt 6 – Nächstes Item anbieten

Nach Abschluss (egal ob Aktionen ausgeführt oder übersprungen):

```
Erledigt. Nächstes Item aus der Liste?
[Zeige die verbleibenden Items aus dem letzten Triage, nummeriert]
```

Wenn keine Items mehr: "Alle Items abgearbeitet. Neues Triage starten?"

---

## Hinweise

- Niemals Kommentare oder Nachrichten **ohne Bestätigung** posten
- Aktionen die übersprungen werden kurz begründen ("kein Kommentar nötig, da...")
- Bei Unsicherheit über Aktionstyp: kurz fragen statt raten
