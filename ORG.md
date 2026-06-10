# 🏢 Org-Übersicht

> **Copilot:** Lade `skills/find-team-issues/SKILL.md` + `skills/find-org-stale/SKILL.md`.
> Update: _"Aktualisiere ORG.md"_ · Zombie-PRs: _"Zeige alle Zombie PRs"_ · Auslastung: _"Team-Auslastung"_
> Persönliche Sicht → [`DASHBOARD.md`](DASHBOARD.md)

> Zuletzt aktualisiert: 2026-06-10 12:00 Uhr
> Org: `atacama-blooms-gmbh-co-kg` · Project Board: Project 9

---

## 🚨 Offene Blocker

> Priorität `🌋 Blocker` in Project 9 (kein `label:blocker` in der Org – nur Project 9 Priority).

> 🟢 Keine Blocker in Project 9 gefunden.

| # | Repo | Titel | Assignee | Inaktiv seit | Link |
|---|------|-------|----------|--------------|------|
| — | — | _keine Blocker gefunden_ | — | — | — |

---

## 👤 Unassigned Issues (Project 9)

> Offene Issues in Project 9 ohne Assignee.

> 109 unassigned Issues (gefiltert auf Project 9). Top-Items:

| #     | Repo        | Titel                                     | Geöffnet   | Link |
|-------|-------------|-------------------------------------------|------------|------|
| #1630 | teamplanner | Bereinigung FTP-Server v. alten Testdaten | 2026-06-09 | [#1630](https://github.com/atacama-blooms-gmbh-co-kg/teamplanner/issues/1630) |

---

## 🔄 PRs ohne Review (Team)

> Offene PRs in Project 9 ohne abgeschlossenen Review.

| PR | Repo | Titel | Autor | Offen seit | Draft | Link |
|----|------|-------|-------|------------|-------|------|
| —  | —    | _noch nicht befüllt_ | — | — | — | — |

---

## 🐌 Stalled In-Progress (Project 9)

> Tickets im Status „In Progress" ohne Aktivität > 21 Tage.

| # | Repo | Titel | Assignee | Inaktiv (Tage) | Link |
|---|------|-------|----------|----------------|------|
| — | —    | _noch nicht befüllt_ | — | — | — |

---

## 🏷️ Triage-Rückstand (Project 9)

> Issues in Project 9 ohne Priorität oder Size – wurden noch nicht triagiert.

| # | Repo | Titel | Geöffnet | Link |
|---|------|-------|----------|------|
| — | —    | _noch nicht befüllt_ | — | — |

---

## 📊 Team-Auslastung

> Assigned Issues/PRs pro Team-Mitglied (Project 9 · In Progress + Todo).

| Mitglied | In Progress | Todo | Gesamt |
|----------|-------------|------|--------|
| —        | —           | —    | _noch nicht befüllt_ |

---

---

# 🧟 Zombie PRs – Org-weit

> Alle offenen PRs in `atacama-blooms-gmbh-co-kg` die seit > 90 Tagen keine Aktivität haben.
> Aktualisiert via: `find-org-stale` Skill

> Zuletzt aktualisiert: 2026-06-10
> **~325 PRs** nicht aktualisiert seit > 90 Tagen · `ansible` archiviert 2026-06-10 ✅ (PRs frozen, nicht mehr actionable)

---

## 📦 Betroffene Repos (Stand 2026-06-10)

> ~~Durchgestrichen~~ = archiviert · Sortiert nach Anzahl aktiver stale PRs

| Repo | Stale PRs | Ältester PR | Hinweis |
|------|-----------|-------------|---------|
| ~~`ansible`~~ | ~~27~~ | ~~2084 Tage~~ | ✅ Archiviert – PRs frozen, kein Handlungsbedarf |
| `avidoc-r` | 25 | 1217 Tage | ⚠️ Neuer Haupt-Problemrepo! |
| `k8s-config-avidoc-r` | 5 | 623 Tage | Davon min. 1 Review-Request an @sebastianhorwege |
| `avidoc-r-api` | 4 | 979 Tage | Vermutlich abandoniert |
| `flower` | 3 | 1077 Tage | |
| `avidoc-r_microtask-ui` | 3 | 754 Tage | |
| `kidohe` | 2 | 1897 Tage | Sehr alte WIP-PRs |
| `avidoc-r-doctagger` | 2 | 1835 Tage | |
| `blooms-utils` | 2 | 1506 Tage | |
| `gradle-codecoverage-template` | 2 | 869 Tage | |
| `model-publishing-*` | 10 | 812 Tage | Diverse model-publishing Repos (je 1 PR) |
| `human-ocr` | 1 | 1603 Tage | |
| `helm-chart-pytorch` | 1 | 1119 Tage | |

---

## 🏆 Top 15: Älteste aktive PRs (ansible ausgeschlossen)

> 🟡 = Draft/WIP · 🔴 = Kein Review, nicht Draft

| PR | Repo | Titel | Autor | Alter (Tage) | Draft | Link |
|----|------|-------|-------|--------------|-------|------|
| #29 | kidohe | [WIP] Secure REST endpoints | @openglfreak | 1897 | 🟡 | [#29](https://github.com/atacama-blooms-gmbh-co-kg/kidohe/pull/29) |
| #1 | avidoc-r-doctagger | Java client library + misc. fixes | @openglfreak | 1835 | 🔴 | [#1](https://github.com/atacama-blooms-gmbh-co-kg/avidoc-r-doctagger/pull/1) |
| #2 | avidoc-r-doctagger | Python client lib | @openglfreak | 1827 | 🔴 | [#2](https://github.com/atacama-blooms-gmbh-co-kg/avidoc-r-doctagger/pull/2) |
| #52 | kidohe | fix: Anzeige Teilnehmer in Kalendarübersicht | @H-CLindner | 1645 | 🔴 | [#52](https://github.com/atacama-blooms-gmbh-co-kg/kidohe/pull/52) |
| #5 | human-ocr | Make docker container build faster | @openglfreak | 1603 | 🔴 | [#5](https://github.com/atacama-blooms-gmbh-co-kg/human-ocr/pull/5) |
| #18 | blooms-utils | fix: add documentbyId | @AuchterSimon | 1506 | 🔴 | [#18](https://github.com/atacama-blooms-gmbh-co-kg/blooms-utils/pull/18) |
| #1074 | avidoc-r | fix: Nebenläufigkeitsproblem DAK | @AuchterSimon | 1217 | 🔴 | [#1074](https://github.com/atacama-blooms-gmbh-co-kg/avidoc-r/pull/1074) |
| #1148 | avidoc-r | Bucket persist | @sebastianhorwege | 1155 | 🟡 | [#1148](https://github.com/atacama-blooms-gmbh-co-kg/avidoc-r/pull/1148) |
| #? | helm-chart-pytorch | — | — | 1119 | — | [Repo](https://github.com/atacama-blooms-gmbh-co-kg/helm-chart-pytorch/pulls) |
| #? | flower | — | — | 1077 | — | [Repo](https://github.com/atacama-blooms-gmbh-co-kg/flower/pulls) |
| #? | avidoc-r-api | — | — | 979 | — | [Repo](https://github.com/atacama-blooms-gmbh-co-kg/avidoc-r-api/pulls) |
| #? | gradle-codecoverage-template | — | — | 869 | — | [Repo](https://github.com/atacama-blooms-gmbh-co-kg/gradle-codecoverage-template/pulls) |
| #? | avidoc-r_microtask-ui | — | — | 754 | — | [Repo](https://github.com/atacama-blooms-gmbh-co-kg/avidoc-r_microtask-ui/pulls) |
