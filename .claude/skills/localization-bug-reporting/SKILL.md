---
name: localization-bug-reporting
description: >
  Turn raw localization QA logs into a categorised developer-facing bug report and then into Jira
  tickets. Use when localization testing is DONE and the findings need reporting: how to split a
  running bug log into category files (FE-top/BE-bottom, one canonical home per bug), why Jira
  tickets must be grouped by fix-unit rather than by category file, the fix-order dependency that
  layout and wire-up tickets create, the developer-instruction block that prevents the four most
  likely wrong fixes, the AC-label scheme that makes acceptance-criteria coverage a live JQL query,
  and the Atlassian-MCP specifics for this site (project BUGS/VB, Markdown not wiki markup, no
  attachment upload). For running the tests themselves use `localization-testing` (employee web) or
  `dashboard-localization-testing` (admin dashboard).
---

# Localization bug reporting — logs → report → Jira

This skill covers the **reporting** half of a localization engagement, for **either surface**. The testing
half lives in `localization-testing` (employee Fit web) and `dashboard-localization-testing` (admin
dashboard) — go there to *find* bugs; come here to *report* them.

**Which surface am I reporting?** It changes the paths, the bug IDs and the FE/BE picture:

| | Admin dashboard | Employee Fit web |
|---|---|---|
| Docs root | `Localization/dashboard/` | `Localization/web/` |
| Bug IDs | `OV#1`, `CC#2`, `RPT#4`… | `B1`…`B28` (sequential) |
| Source of record | `bugs/logs/bug-log.md` | `bugs/bug-log.md` |
| Backend defects | **0** — backend out of scope, English expected | **5 confirmed** (B14, B23, B24, B26, B27) |
| Dictionary claim | Complete, 991 keys × 18 — assertable | **Not** assertable (JSON returns the SPA shell) |

**Worked reference — the admin-dashboard engagement, 2026-07-29:** ~76 FE bugs, 12 `[FE-BE TBD]`, 0 confirmed
BE, 19 modules, 18 languages → **12 report files → 13 Jira tickets (VB-349…VB-361)**. Artifacts:
`Localization/dashboard/bugs/` (report), `JIRA-FILING-GUIDE.md` (model), `JIRA-TICKETS-READY-TO-PASTE.md`
(bodies), `JIRA-FILED.md` (what was filed). The employee-web engagement has **not** yet been through this
pipeline — its 28 bugs are logged but not categorised or filed.

---

## 0. Do this before you report anything

**Re-open the evidence and check it against the claims.** This is not a formality — it catches real errors.

In the reference engagement, mapping screenshots to tickets meant opening
`create-challenge_de_step_review.png`, which turned out to **be** the wizard Review step. Four files claimed
"step 5 never reached, in any language". Wrong: it *was* reached via the **pre-built template** path (which
pre-fills tasks and bypasses the drag-and-drop gate) and it had already produced a logged bug. Filing would
have put a false blocked-item claim in front of the team.

Rules:
1. **Open every screenshot you are about to cite.** A filename is not evidence; the image is. Filenames
   drift from their content (`_step5.png` was actually step 4).
2. **Grep the per-module logs for the claim you are about to make.** The detail usually already exists in
   `logs/<module>.md` and contradicts the summary.
3. **Fix the source files first, then the report, then file.** Never file a ticket you know is wrong "because
   the doc says so".

---

## 1. Two-layer document model — keep authority explicit

```
bugs/
  00-INDEX.md … 11-*.md    ← DERIVED, dev-facing curated report
  logs/
    bug-log.md             ← SOURCE OF RECORD. New findings append HERE first
    <module>.md            ← per-module detail
    <run>.md               ← point-in-time run records, not maintained after the run
```

- New findings go into **`logs/bug-log.md` first**, always.
- The numbered files are a **derived view** regenerated from it. **If they disagree, the log wins.**
- Write a `README.md` in `logs/` stating this, and **banner superseded run files** rather than deleting them
  (`> ⚠️ SUPERSEDED — see X.md`). Point-in-time records are worth keeping; silently stale ones are not.

---

## 2. The category files

One file per **defect type**, each split **`═══ FRONTEND ═══` on top / `═══ BACKEND / SOURCE NEEDS TRIAGE ═══`
at the bottom**.

| File | Contents |
|---|---|
| `00-INDEX.md` | FE/BE totals · severity counts · **ticket-AC mapping** · per-file explanation · language tiers · module coverage · full checklist with pass/fail · what is NOT covered |
| `01-P1-P2-CRITICAL.md` | All P1+P2, ordered by **fix leverage**. **The only file allowed to repeat bugs** |
| `02-UNTRANSLATED.md` | Wire-up gaps and not-externalised strings |
| `03-UI-LAYOUT.md` | Clip / spill / truncation / overlap / broken images / RTL |
| `04-LOCALE-FORMATTING.md` | Date · time · number · currency · numeral systems |
| `05-LINGUISTIC-QUALITY.md` | Register/tone · pronouns · terminology · casing · mixed-language fragments |
| `06-FUNCTIONAL.md` | Validation · CRUD · toasts · dialogs · wizard · persistence · search |
| `07-ACCESSIBILITY.md` | `lang` · `alt` · accessible names · dialog semantics · focus |
| `08-ENHANCEMENTS.md` | P4, suggestions, judgment calls |
| `09-NOT-A-BUG.md` | Investigated and cleared, **with the reason** |
| `10-BLOCKED-NEEDS-DECISION.md` | Blocked on data/env + open product questions |
| `11-*.md` | Per-AC evidence files as needed |

**Two hard rules:**
- **Uniqueness.** Every bug has exactly **one canonical home** outside `01`. Bugs repeated in `01` are tagged
  `⚠️ ALSO IN 01 — fix there first` in their type file. *Verify this mechanically* — a first pass produced two
  violations (one bug in both `02` and `08`, another in both `05` and `08`).
- **`09-NOT-A-BUG.md` is a deliverable, not filler.** It is what stops cleared items being re-filed. Attach it
  to the parent ticket.

---

## 3. Jira tickets ≠ report files

**Do NOT file one ticket per category file.** Three reasons, all learned the hard way:

1. **The P1/P2 file is a priority *view*, not a work package.** Its bugs deliberately repeat from the type
   files. A ticket for it double-files them, and two devs land on the same bug.
2. **Several files are not dev work.** Cleared items (nothing to fix), AC evidence (mostly PASS), and
   QA/product follow-ups all pollute a bug queue.
3. **Category ≠ fix unit.** One date formatter caused **7** symptoms sitting in one file; one shared filter
   component caused **5** spread across three files. Group by *what a developer changes*.

**The model that works:**

```
root-cause tickets   ← cross-cutting, one component each, highest leverage. File FIRST
category tickets     ← the remainder, grouped by what a dev touches
non-dev tickets      ← copy/product task · a11y (own epic) · QA follow-up
evidence-only        ← index, priority view, cleared items, AC evidence → attach to parent
```

Reference split: **5 root-cause + 6 category + 2 non-dev = 13**, from 12 files.

**Split by effort inside a category when it differs.** "Dictionary exists but isn't wired" (a one-line fix)
and "no i18n keys exist at all" (externalise everything first) are different tickets even though both render
English.

---

## 4. The fix-order dependency — check for this every time

**A wire-up fix can create a layout bug.** When a string is untranslated, the container holds *English* — which
is usually the shortest form and happens to fit. Translate it and it overflows.

In the reference engagement: the audience operator box is **50px** (`is in` fits; Indonesian `termasuk dalam`
needs **+55px**) and the Wellness Leagues chips are **110px** (Hungarian `Alkalmazotti azonosító` needs
**+119px**). German and Spanish "passed" *only* because they left the string as English.

**The tell:** a component that clips **only on the surfaces where the wire-up already works**. That surface is
a live preview of what the others will look like after the fix.

**So:** file the container-widening ticket first and add a real Jira **`blocks`** link to each wire-up ticket.
A note in the description is not enough — a dev picking up the wire-up ticket in isolation will ship six newly
clipping surfaces.

---

## 5. Developer instructions — paste into every ticket

These four prevent the most likely wrong fixes:

> **Scope: localization is frontend-only today.** The backend is not translated yet, so backend-served English
> (activity master lists, challenge status/type, report cell data, country/gender lists, email-template
> content) is **expected** — do not "fix" it. `accept-language` is already sent correctly, so backend English
> is a **scope decision, not a missing-header bug**.
>
> **There are no missing translations** *(admin dashboard — verify before asserting this on another
> surface)*. All 18 dashboard dictionaries are complete: **991 keys each, 0 missing, 0 empty**. The fix is
> almost never "add a translation" — it is **use the key that already exists**. Check
> `/assets/i18n/fit/<lang>.json` before adding anything.
>
> On the **employee Fit web** this claim does **not** hold as written: `/ng/assets/i18n/fit/<lang>.json`
> returns the SPA shell rather than JSON, so completeness cannot be asserted from the dictionary — and that
> surface **does** have confirmed backend defects. Classify against the English baseline and API bodies
> instead.
>
> **Two repro traps:** (a) switch language then **reload the route** — an in-place switch leaves stale strings
> and will make a broken fix look like it worked; (b) verify by **direct URL**, not sidebar clicks — some
> components render English cold and correct after in-app nav. Cold is what users get from a bookmark.
>
> **Tenant:** India server, company **355 (UAT)**. `localStorage.fit_lang` holds the selection.
> `<html lang>` stays `"en"` regardless — its own bug; don't use it to check state.

**Definition of done, per ticket:** fixed and verified in **de + ar + zh-CN** minimum (German for text
expansion, Arabic for RTL, Chinese for the short-string case) · verified on a **cold load by direct URL** ·
at **1440 and 1920** for anything layout-related · recorded in `Regression_Report.md`.

---

## 6. Keep non-localization findings OUT of the count

Found *during* localization testing ≠ caused by localization. Filing these under localization distorts the
metric that will be read as a verdict on the i18n rollout.

| Item | What it is | Where |
|---|---|---|
| Breaks in English too | Responsive bug | Responsive/UI backlog |
| Overlay/z-index blocking a CTA | UI bug | UI backlog |
| Broken images / malformed CDN URLs | Asset issue, source unproven | Triage first |
| `alt` text, focus, dialog semantics, contrast | Pre-existing a11y debt | **A11y epic — label `accessibility`, NOT `i18n`** |
| Missing feature | Product gap | Product backlog |
| Overflowing **authored content** titles | Content length, not translation | Not a bug |

The one a11y item that **is** localization: **`<html lang>` not reflecting the locale** — it breaks
screen-reader pronunciation for every non-English language.

**Separate localization from responsive by measuring English as a control.** Without it, one bug hides inside
the other — a single overflow finding split cleanly into a localization bug (German only) and a responsive bug
(English overflowed too) once English was measured.

---

## 7. Atlassian MCP — site specifics

**Site:** `vantagecirclejira.atlassian.net` (pass the hostname as `cloudId` directly).
**Bug project:** `BUGS` = key **`VB`** (types: Bug, Task, Improvement, New Feature, Epic).
**Testing tickets:** project `VF` (Vantage Fit) — e.g. VF-2207.
**Auth:** the connector is a claude.ai connector — the **user** must run `/mcp` → "claude.ai Atlassian".

**Gotchas:**
- **Use Markdown, not Jira wiki markup.** With `contentFormat: "markdown"`, `h2.` and `||header||` are stored
  as literal text. Use `##` and standard `|---|` tables. `**bold**` not `*bold*` (single `*` = italic).
- **No attachment upload.** The connector creates/edits issues, comments and links — it cannot upload files.
  **List exact screenshot filenames under an "Evidence" heading in each ticket** and tell the user to
  drag-drop from `evidence/`. Say this *before* filing, not after.
- **Task lists don't render.** `- [ ] item` becomes literal `[ ]`. Still useful as a checklist, just not
  tickable.
- **Cross-project links work.** Bugs in `VB` link to a story in `VF` with **`Relates`** (not `Blocks` — the
  bugs don't block the test story from closing).
- **`Blocks` direction:** `inwardIssue` = the blocker, `outwardIssue` = the blocked issue.
- **JQL search output can blow the token limit.** Request narrow `fields` and post-process with `jq` on the
  saved file rather than reading it back.
- **Leave `assignee` unset** unless told otherwise.

---

## 8. Labels — make AC coverage a live query

```
fit-admin · i18n                     ← every ticket
i18n-rootcause                       ← the cross-cutting fix-first set
i18n-ac1-switch · i18n-ac2-strings · i18n-ac3-fallback ·
i18n-ac4-layout · i18n-ac5-persist   ← one per acceptance criterion
accessibility                        ← a11y tickets, WITHOUT i18n
needs-backend · copy · qa-followup   ← routing hints
```

```
labels = i18n AND labels = fit-admin ORDER BY priority DESC
labels = i18n-rootcause AND status != Done
labels in (i18n-ac1-switch, i18n-ac2-strings, i18n-ac4-layout, i18n-ac5-persist) AND status != Done
```

**Severity mapping:** P1 → Highest · P2 → **High** · P3 → Medium · P4 → Low.

---

## 9. The parent-ticket summary comment

Post one comment on the testing ticket carrying: coverage (modules × languages × widths × tenant) · the
ticket range · FE/BE/cleared totals · severity counts · **a per-AC table naming the ticket that carries each
AC** · the fix-order dependency · and the honesty section.

**Three things that must be stated, not buried:**

1. **"Zero P1" is a tested result, not an untested gap** — name the leads that were executed and their
   outcomes. Otherwise it reads as thin testing.
2. **What fraction of findings sits outside the ACs.** In the reference engagement ~60% (locale formatting,
   a11y, RTL, register, CRUD) was outside the five ACs. That is additional value — report it **separately**
   so it isn't mistaken for AC evidence.
3. **Whether this is a sign-off.** It usually isn't. Name what's untouched (other servers, timezone, unused
   widths) and whether regression verification has begun.

**Never round a partial pass up to a pass.** "Fallback works when the whole file is missing, but the
single-missing-key case is unproven" is the honest form. Split the checklist row rather than claiming the
dimension.

---

## 10. Close the loop

Write a `JIRA-FILED.md` back into the bugs folder: ticket key → title → **source report file** → FE/BE nature
→ screenshots to attach, plus the dependency links and the tracking JQL. It's how anyone reading the repo
later finds the tickets, and how anyone reading a ticket finds the evidence.

Then commit. The repo and Jira should never disagree about what was filed.
