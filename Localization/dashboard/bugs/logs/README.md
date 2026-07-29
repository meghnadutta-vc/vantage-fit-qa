# `logs/` — raw working records (admin dashboard localization)

33 files, ~4,750 lines. This folder is the **working layer**. The dev-facing deliverable is the curated
report one level up: [`../00-INDEX.md`](../00-INDEX.md).

## Authority

| Rank | File | Role |
|---|---|---|
| 1 | **`bug-log.md`** (1,279 lines) | **SOURCE OF RECORD.** Every bug in full, with addenda from all 22 runs. New findings append **here first**. |
| 2 | `../00-INDEX.md` + `../01`–`11` | **Derived view** — the same bugs recategorised for the dev team. If it disagrees with `bug-log.md`, the log is right. |
| 3 | `<module>.md` (13 files) | Per-module bug detail. Feeds into `bug-log.md`. |
| 4 | `<run>.md` (19 files) | **Point-in-time run records.** Not maintained after their run. Cite for method and evidence, not for current status. |

## Per-module logs (13)

`overview.md` · `create-challenge.md` · `manage-challenge.md` · `reports.md` · `settings.md` ·
`add-employees.md` · `preview-emails.md` · `content-library.md` · `create-content.md` ·
`community-events.md` · `create-announcement.md` · `send-custom-email.md` · `email-designer.md` ·
`workforce-health.md`

## Per-run logs (19) — what each run actually did

| File | Run |
|---|---|
| `ui-break-sweep-de.md` (417 L) | German UI-break sweep, `scrollWidth > clientWidth` detection, CLIP/SPILL/SCROLL classification |
| `full-checklist-2res-run13.md` | ⚠️ *partial/historical* — started the full-checklist re-run at 1440 + 1920 |
| `spanish-full-sweep.md` | Spanish across the 14 unmeasured modules; found ES#1 (cold-load vs in-app switch) |
| `desktop-1920-de-es-crud.md` | Desktop 1920, de + es, all modules + CRUD |
| `p1-hunt-g5-g6-g4.md` | P1 hunt: comma-decimal input → CSV non-ASCII → export contents |
| `multilang-fr-pt-pl-zh.md` | fr / pt / pl / zh-CN deep tier |
| `deep-tier-arabic.md` | Arabic RTL deep tier |
| `deep-tier-remaining-10.md` | The 10 shallow-tier languages brought to deep |
| `all-18-languages.md` | All 18 shipped languages opened + measured |
| `f5-f7-f8-results.md` | ✅ **authoritative** F5 dialogs / F7 wizard / F8 persistence |
| `f5-f7-f8-attempt.md` | ⚠️ **SUPERSEDED** by `f5-f7-f8-results.md` (network outage cut the run) |
| `u9-register-glossary-all-languages.md` | U9 register + glossary pass, all 16 remaining languages |
| `french-spanish-pass.md`, and others | earlier per-language passes |

## Conventions

- **Append, never overwrite.** Prior runs stay readable.
- Every bug is tagged **[FE] / [BE] / [FE-BE TBD]** — localization is frontend-only today, so
  backend-served English is *expected* and must be identified, not filed as a defect.
- Screenshots are referenced as `../../evidence/<name>.png` from this folder.
- A result is **point-in-time** (gap G1): a screen that passes early in a session can regress later.
