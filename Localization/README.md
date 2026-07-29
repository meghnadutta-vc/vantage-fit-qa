# Localization QA — Vantage Fit

All localization (i18n) testing for Vantage Fit lives here, for **both** surfaces. This is the only
topic-first folder in the repo (everything else is platform-first) — grouped this way because the same
languages, the same dictionaries, and the same recurring defect patterns span both products.

## Which surface am I looking at?

| | **`dashboard/`** | **`web/`** |
|---|---|---|
| Product | Fit **admin** dashboard (HR admin) | Fit **employee-facing** web |
| URL | `dashboard-v2.vantagecircle.co.in/fit/*` | `app.vantagecircle.co.in/ng/fit/*` |
| Modules | 19 | 5 (Summary, Challenges, Programs, Community, Diary/Trends) |
| Bug IDs | module-prefixed — `OV#1`, `CC#2`, `RPT#4`… | sequential — `B1`…`B28` |
| Languages | 18 shipped, 4 depth tiers | de / fr / es / pt passes |
| Skill | `dashboard-localization-testing` | `localization-testing` |
| Driver | Playwright MCP | Playwright MCP |

## Where the bugs are

**Every bug MD file for a surface is inside that surface's `bugs/` folder.**

### `dashboard/bugs/` — start at [`00-INDEX.md`](dashboard/bugs/00-INDEX.md)

12 categorised report files, each split **frontend on top / backend at the bottom**:

| File | Contents |
|---|---|
| `00-INDEX.md` | **Read first.** FE/BE totals, ticket-AC mapping, per-file explanation, language + module coverage, full U/F/A checklist status |
| `01-P1-P2-CRITICAL.md` | 0 P1 · 19 P2, ordered by fix leverage. **The only file allowed to repeat a bug** — everything here is cross-referenced as a repeat elsewhere |
| `02-UNTRANSLATED.md` | English strings on a localized screen (wire-up gaps, not-externalised) |
| `03-UI-LAYOUT.md` | Truncation, clipping, spill, overlap |
| `04-LOCALE-FORMATTING.md` | Date / time / number / separator / currency |
| `05-LINGUISTIC-QUALITY.md` | Register/tone, pronouns, terminology consistency, context coherence |
| `06-FUNCTIONAL.md` | Interaction, validation, CRUD, dialogs, wizard, persistence |
| `07-ACCESSIBILITY.md` | `<html lang>`, labels, contrast, focus |
| `08-ENHANCEMENTS.md` | Polish / parity gaps — not defects |
| `09-NOT-A-BUG.md` | Investigated and ruled out, with the reason (so nobody re-files them) |
| `10-BLOCKED-NEEDS-DECISION.md` | Blocked flows + open product questions |
| `11-AC3-FALLBACK.md` | Ticket AC3 (missing-translation fallback) test results |

`dashboard/bugs/logs/` holds the 33 raw working files — see its own
[README](dashboard/bugs/logs/README.md) for what is authoritative there.

### `web/bugs/`

`bug-log.md` (consolidated, B1–B28) plus per-module logs: `challenges.md`, `community.md`,
`diary-trends.md`, `programs.md`, `summary.md`.

## Authority — which file wins

1. **`dashboard/bugs/logs/bug-log.md`** is the **source of record** for the admin dashboard. Every bug,
   in full, with the addenda from all runs. New findings get appended **here first**.
2. **`dashboard/bugs/00-INDEX.md` + `01`–`11`** are a **derived, curated view** of that log — reorganised
   for the dev team by category and severity. If the two ever disagree, the log is right and the report
   needs regenerating.
3. Per-run files in `logs/` are **point-in-time records**. They are not maintained after their run; two
   carry banners saying so.

## Read before claiming the engagement is complete

[`dashboard/GAP_REGISTER.md`](dashboard/GAP_REGISTER.md) — **G1–G26**, the known coverage gaps
(unreviewed screenshots, untested servers, export contents, timezone, and more). The engagement docs say
"India-server module coverage complete", which is true **per module** and misleading **per dimension**.

## Other files per surface

`test-cases/<module>.md` · `evidence/` (dashboard 91, web 58 screenshots) ·
`Execution_Status.md` · `Coverage_Matrix.md` · `coverage-log.md` · `Notes.md` ·
`Localization_Test_Plan.md` · `Regression_Report.md`

## `_superseded-dashboard-first-pass/`

An **abandoned earlier attempt** at the admin-dashboard engagement (formerly `dashboard/localization/`).
Kept only because its check-ID system was worth salvaging — that has already been merged into the
`dashboard-localization-testing` skill. **Do not add to it and do not cite it.**
