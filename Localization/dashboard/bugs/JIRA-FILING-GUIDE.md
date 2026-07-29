# Jira filing guide — Fit Admin Dashboard localization

How to turn the 12 report files in this folder into Jira tickets, what to tell developers, and how to
track closure. Compiled 2026-07-29.

**Source of truth for bug detail:** `logs/bug-log.md`. The category files (`00`–`11`) are the curated view.
Jira tickets should link to the **category file** as evidence and quote bug IDs.

---

## 1. Ticket model — 13 tickets, not 12

**Do NOT file one ticket per MD file.** Three reasons:

1. **`01-P1-P2-CRITICAL.md` is a priority *view*, not a work package.** Every bug in it is deliberately
   repeated from files 02–07. A ticket for 01 double-files 19 bugs.
2. **`09`, `10`, `11` are not dev work.** 09 = cleared items (nothing to fix), 11 = AC3 test evidence
   (mostly PASS), 10 = QA/product follow-ups.
3. **Category ≠ fix unit.** One date formatter causes 7 symptoms in file 04; one shared filter component
   causes 5 across files 01 and 02. Group by *what a dev changes*.

So: **5 root-cause tickets** (cross-cutting, highest leverage) + **6 category tickets** (the remainder) +
**2 non-dev tickets**. Each bug ID appears in **exactly one** ticket.

---

## 2. The tickets

Prefix everything `[Fit Admin][i18n]` so the whole set is one JQL query. Link every one to the parent
localization testing ticket with **"relates to"** (not "blocks" — they don't block the test ticket closing).

### Fix-first: root-cause tickets

| # | Title | Bug IDs | Evidence file | Sev |
|---|---|---|---|---|
| **1** | `[Fit Admin][i18n] Locale-unaware date/time formatter renders English months and wrong patterns in all 18 languages` | U7#1, U7#3, CC#2, RPT#4, OV#5, EV#2, date-input format | `04-LOCALE-FORMATTING.md` | High (P2 aggregate) |
| **2** | `[Fit Admin][i18n] Shared report filter and column-selector control renders hardcoded English defaults` | RPT#1, RPT#2, ES#3, OV#2, CL#2 | `01`, `02`, `03` | High (P2) |
| **3** | `[Fit Admin][i18n] Selected language is not applied on cold load or on in-place switch (init-order race + stale strings)` | ES#1, ES#2, OV#7 | `01`, `02`, `06` | High (P2) — **AC1** |
| **4** | `[Fit Admin][i18n] Arabic is fully translated but renders left-to-right — RTL layout not implemented` | AR#1 (+ RTL a11y, blocked) | `01`, `03`, `07` | High (P2) |
| **5** | `[Fit Admin][i18n] Admin language preference is client-side only — not persisted server-side across sessions` | F8#1 | `01`, `06` | High (P2) — **AC5** |

> Tickets 1–5 are where the leverage is: ~20 symptoms, 5 code changes. Sequence them first.

### Category tickets

| # | Title | Bug IDs | Evidence file | Sev |
|---|---|---|---|---|
| **6** | `[Fit Admin][i18n] Modules shipped with no or unwired i18n keys — render entirely/partly in English` | ANN#1, ANN#2, CRC#1, CRC#2, ED#1, WS#1, OV#1 | `01`, `02` | High (P2) |
| **7** | `[Fit Admin][i18n] Individual wire-up gaps — translation exists in the dictionary but is not rendered` | CC#1, CC#3, CC#5, CL#1, CL#3, OV#3, RPT#3, WL#1, UP#1, UP#6, AE#1, EV#1, ANN#3, DF#1, AE#2, UP#2, FRCA#1 | `02-UNTRANSLATED.md` | Medium (P3) |
| **8** | `[Fit Admin][i18n] Text clipping and spill in long-word languages — 4 zero-headroom containers break at every width` | PN#1, PN#2, WS#2, WL#2, ES#4, HU#1, RU#1 + viewport-dependent set | `03-UI-LAYOUT.md` | Medium (P3) — **AC4** |
| **9** | `[Fit Admin][i18n] Numeral system and currency formatting incorrect per locale` | AR#3, AR#2, U7#2 | `04-LOCALE-FORMATTING.md` | Medium (P3) |
| **10** | `[Fit Admin][i18n] Silent write failures and diacritic-blind search` | UP#4, UP#5, SET#4, UP#7, U8/G8, F6#1 | `06-FUNCTIONAL.md` | High (UP#4/#5 = P2) |
| **11** | `[Fit Admin][i18n][Copy] No product-wide register/tone policy — formality mixes within languages` | REG#1, REG#2, REG#3, AR#5, TERM#1, TERM#2, RPT#7 | `05-LINGUISTIC-QUALITY.md` | Medium (P3) |

### Non-dev tickets

| # | Title | Type | Evidence file |
|---|---|---|---|
| **12** | `[Fit Admin] Accessibility defects found during localization testing — html lang, alt text, dialog semantics, icon button names` | Bug — **route to a11y epic, not localization** | `07-ACCESSIBILITY.md` |
| **13** | `[Fit Admin][i18n] QA follow-up — blocked coverage, source triage, and open product decisions` | Task, assign **QA/Product** | `10-BLOCKED-NEEDS-DECISION.md`, `08-ENHANCEMENTS.md` |

### Not tickets — link as evidence on the parent only

`00-INDEX.md` (the report front door) · `01-P1-P2-CRITICAL.md` (priority view) ·
`09-NOT-A-BUG.md` (**attach this — it stops re-filing cleared items**) · `11-AC3-FALLBACK.md` (AC3 evidence).

---

## 3. Keep these OUT of the localization count

Filing these under localization will distort the localization bug metrics — they were **found during** this
testing but are not localization defects:

| Item | What it actually is | Where to file |
|---|---|---|
| Responsive defects section in `03` | Break in English too | Responsive/UI backlog |
| MGC#2 chatbot overlay blocks "Update Challenge" | Z-index / overlay bug | UI backlog — **P2-ish, don't lose it** |
| MGC#4, EV#3 broken images (17 total) | Asset/CDN, `[FE-BE TBD]` | Needs source triage first |
| Most of `07-ACCESSIBILITY.md` | Pre-existing a11y debt | A11y epic (ticket 12) |
| DEL#1 no delete control for challenges | Missing feature | Product backlog |

---

## 4. What to put in every ticket (developer instructions)

Paste this block into each ticket, or once into the parent and link it. **These four points prevent the
most likely wrong fixes.**

> **Scope: localization is frontend-only today.** The backend is not translated yet, so backend-served
> English (activity master lists, challenge status/type, report cell data, country/gender lists,
> email-template content) is **expected** — do not "fix" it. `accept-language` is already sent correctly;
> backend English is a backend-scope decision, not a missing-header bug.
>
> **There are no missing translations.** All 18 dictionaries are complete: **991 keys each, 0 missing,
> 0 empty**. So the fix is almost never "add a translation" — it is **use the key that already exists**.
> Dictionaries: `/assets/i18n/fit/{en,de,fr,es,…}.json`. Before adding a key, check it isn't already there.
>
> **How to reproduce — two traps:**
> 1. Switch language via the sidebar `<select>`, then **reload the route**. An in-place switch leaves stale
>    strings (that's bug OV#7) and will make you think a fix worked when it didn't.
> 2. Verify by **direct URL**, not by clicking sidebar links. Some components render English on a cold load
>    and correctly after in-app navigation (bug ES#1). The cold state is what users get from a bookmark.
>
> **Tenant:** India server, company **355 (UAT)**. `localStorage.fit_lang` holds the selection.
> Note `<html lang>` stays `"en"` regardless — that's its own bug (OV#4), don't use it to check state.

Add per ticket type:

- **Ticket 8 (layout):** a defect means `scrollWidth > clientWidth`. Classified CLIP (`overflow:hidden`,
  text cut) / SPILL (`overflow:visible`, text collides) / **SCROLL (`overflow:auto` — NOT a defect,
  don't fix)**. The four width-independent containers (150px, 113px, 110px, 50px) break at **every**
  resolution — those are the real ones. Verified against English controls to exclude responsive bugs.
- **Ticket 11 (copy):** this needs a **product decision first, then content work** — not a dev fix.
  Vantage Fit's default voice is informal (`du`/`tu`/`je`). Decide once, apply across all 18 dictionaries.
- **Ticket 13:** includes **12 `[FE-BE TBD]` items whose source is unproven** — these need a dev/product
  call before they can be assigned to anyone.

---

## 5. Definition of done (put this in each ticket)

1. Fixed in **de + ar + one CJK (zh-CN)** at minimum — de for text expansion, ar for RTL, zh-CN for the
   short-string case. A fix verified only in English is not verified.
2. Verified on a **cold load by direct URL**, not just after in-app navigation.
3. Verified at **1440 and 1920** for anything layout-related.
4. QA re-verifies and records it in `Localization/dashboard/Regression_Report.md` (**currently empty —
   dozens of bugs, zero re-verifications; this is the biggest process gap**).

---

## 6. Tracking

### Labels
Apply to every ticket: `fit-admin`, `i18n`.
Then one AC label where applicable, so AC coverage is reportable directly from Jira:

| Label | AC | Tickets |
|---|---|---|
| `i18n-ac1-switch` | Switching language updates the UI | 3 |
| `i18n-ac2-strings` | No untranslated / raw-key strings | 6, 7 |
| `i18n-ac3-fallback` | Fallback when a translation is missing | — (evidence only, `11`) |
| `i18n-ac4-layout` | No layout breakage per language | 8 |
| `i18n-ac5-persist` | Preference persists across sessions | 5 |

Plus `i18n-rootcause` on tickets 1–5 so the fix-first set is one filter.

### JQL to keep handy
```
labels = i18n AND labels = fit-admin ORDER BY priority DESC
labels = i18n-rootcause AND status != Done
labels in (i18n-ac1-switch, i18n-ac2-strings, i18n-ac4-layout, i18n-ac5-persist) AND status != Done
```

### Per-bug closure inside a ticket
Tickets 7 and 8 carry 17 and ~12 bug IDs. A single status field can't express partial progress, so:
**put a checklist of bug IDs in the description** (`- [ ] CC#1 — 5 challenge-type cards`) and have the dev
tick them. Only make subtasks if your team splits them across people.

### Reporting AC completion on the parent ticket
Two things to state honestly, both already documented in `00-INDEX.md` §2b:

- **~60 % of the findings are outside the 5 ACs** (locale formatting, a11y, RTL, register, CRUD). That's
  extra value — report it separately so it doesn't read as AC evidence.
- **AC3 passes for the whole-file-missing case but the single-missing-key case is not proven**, and
  **AC5's literal logout→login leg was not performed** (dashboard-v2 exposes no logout control). Don't
  claim either as a clean pass.

### Still open — flag on the parent, not as bugs
US / Europe / E2E servers (**0 of 19 modules** — locale formatting and timezone are exactly what varies
per server), G7 timezone (0/19), 768/375 widths, G4 export contents (blocked on data), wizard step 5 custom path
(needs manual drag; the template path was completed in German). **This report is not a sign-off.**
