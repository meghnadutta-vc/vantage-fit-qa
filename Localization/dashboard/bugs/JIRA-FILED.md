# Jira tickets filed — Fit Admin Dashboard localization

Filed 2026-07-29 into project **BUGS (`VB`)**. Parent testing ticket: **VF-2207** (project Vantage Fit,
"[VF_Testing] Localisation on admin Fit — language switch, string coverage, fallback").

All 13 are linked to VF-2207 with **"relates to"**.

## 2026-07-30 — rewritten for a non-project audience

Every ticket was **retitled and its description rewritten** so someone outside the project can read it:

- **Titles carry no jargon and no bug IDs.** `[Fit Admin][i18n] Locale-unaware date/time formatter…` became
  *"Fit Admin — Dates always show English month names, in every language"*. Internal refs (`OV#12`, `RPT#1`)
  survive **only** inside the technical section, where a developer needs them to cross-reference.
- **Every description now opens with "What is wrong (plain summary)"** — the defect in ordinary language,
  then "why it matters", then the proof under a `# Technical detail` divider.
- **Dead references removed:** the "Screenshots to attach:" instruction line and the `Repo:` path (neither
  is actionable for a reader without repo access).
- **Each ticket now carries the source MD file as a comment**, prefixed *"Refer to the QA document below"*,
  so the evidence lives in Jira rather than only in this repo. Where a file feeds several tickets it is
  pasted **in full on one** and cross-referenced from the others — noted in each comment.
- **Each ticket also carries a "Backend findings are also available" comment**, tailored per ticket, naming
  `BACKEND-BUGS.md` (23 findings, employee web) and — where relevant — the unproven-source items.

**Titles as they now stand in Jira:**

| Ticket | Type | Pri | Title | Source file(s) | FE / BE |
|---|---|---|---|---|---|
| [VB-349](https://vantagecirclejira.atlassian.net/browse/VB-349) | Bug | High | Text gets cut off in some languages because four boxes are too narrow | `03-UI-LAYOUT.md` | **FE** |
| [VB-350](https://vantagecirclejira.atlassian.net/browse/VB-350) | Bug | High | Dates always show English month names, in every language | `04-LOCALE-FORMATTING.md` | **FE** |
| [VB-351](https://vantagecirclejira.atlassian.net/browse/VB-351) | Bug | High | Report filters stay in English on all 6 reports, even when another language is selected | `02`, `01`, `03` | **FE** |
| [VB-352](https://vantagecirclejira.atlassian.net/browse/VB-352) | Bug | High | Chosen language is not applied properly: English on first page load, and old text left behind after switching | `06`, `01` | **FE** |
| [VB-353](https://vantagecirclejira.atlassian.net/browse/VB-353) | Bug | High | Arabic is fully translated but the page layout is not flipped right-to-left | `01`, `03`, `07` | **FE** |
| [VB-354](https://vantagecirclejira.atlassian.net/browse/VB-354) | Bug | High | The admin's chosen language is forgotten on a different browser, device or private window | `06`, `01` | **FE bug, needs BE work** |
| [VB-355](https://vantagecirclejira.atlassian.net/browse/VB-355) | Bug | High | Several whole pages stay in English in every language | `02`, `01` | **FE** |
| [VB-356](https://vantagecirclejira.atlassian.net/browse/VB-356) | Bug | Medium | Checklist of individual labels that stay in English even though the translation already exists | `02-UNTRANSLATED.md` | **FE** |
| [VB-357](https://vantagecirclejira.atlassian.net/browse/VB-357) | Bug | Medium | Numbers and currency are shown in the wrong format for the selected language | `04-LOCALE-FORMATTING.md` | **FE** + 3 unproven-source |
| [VB-358](https://vantagecirclejira.atlassian.net/browse/VB-358) | Bug | High | Saving can fail without telling the admin anything, and search misses names with accents | `06-FUNCTIONAL.md` | **FE** (BE is correct) |
| [VB-359](https://vantagecirclejira.atlassian.net/browse/VB-359) | Task | Medium | The product speaks to admins politely in one language and casually in another; nobody ever decided which | `05-LINGUISTIC-QUALITY.md` | **Content/product** |
| [VB-360](https://vantagecirclejira.atlassian.net/browse/VB-360) | Bug | Medium | Accessibility gaps: screen readers read the wrong language, images have no descriptions, dialogs are not announced | `07-ACCESSIBILITY.md` | **FE** |
| [VB-361](https://vantagecirclejira.atlassian.net/browse/VB-361) | Task | Medium | Follow-up list: what testing could not cover, 8 decisions product needs to make, and leftover test data to clean up | `10`, `09`, `11`, `08` | **Triage** |

Every title is prefixed `Fit Admin — ` in Jira (dropped from the table above for width).

## Assignees and labels — changed by someone else after filing

All 13 were filed **unassigned by design**. As of 2026-07-30, **VB-349 → VB-354 are assigned to Maseudul
Hussain and their labels have been stripped**; VB-355 → VB-361 still carry `fit-admin` / `i18n` / AC labels.
**That breaks the AC-coverage JQL below for the first six tickets.** All 13 remain **To Do**.

## Blocking dependency (encoded in Jira)

**VB-349 blocks VB-351 and VB-356.** The audience operator box (50px) and Wellness Leagues chips (110px) fit
today *only because their text is still untranslated English*. Ship the wire-up fixes first and the text
grows and clips across six report surfaces.

## Files NOT filed as their own tickets

Each is now **posted as a comment** on the ticket(s) it supports, so nothing is repo-only:

| File | Why not a ticket | Where it now lives in Jira |
|---|---|---|
| `00-INDEX.md` | Report front door | not posted — superseded by the per-ticket plain-language summaries |
| `01-P1-P2-CRITICAL.md` | **Priority view** — all 19 bugs deliberately repeat from 02–07. Filing it would double-file them | relevant entries quoted on VB-349/353/355/356/358 |
| `09-NOT-A-BUG.md` | Cleared items — so nobody re-files the ~14 | VB-361, second comment |
| `11-AC3-FALLBACK.md` | Fallback test evidence (mostly PASS) | VB-361, second comment |
| `08-ENHANCEMENTS.md` | P4 / parity, not defects | VB-361, second comment |

## Screenshots — still must be attached manually

The Jira MCP connector exposes issue creation, editing, comments and links, but **no attachment upload**.
The old "Screenshots to attach:" line was removed from every description (it read as an instruction to the
developer, which it wasn't). Each ticket's **first comment now names its screenshot files** with an explicit
*"ask QA for these"*. Drag them in from `Localization/dashboard/evidence/`.

| Ticket | Screenshots |
|---|---|
| VB-349 | `publishnotif_de.png`, `wellnessleagues_de.png`, `india_overview_de_1440_glance_overflow.png`, `india_settings_de_1024_break.png`, `india_mgc_de_1024_card_overflow.png` |
| VB-350 | `de_1920_datepicker_english_calendar.png`, `create-challenge_de_datepicker.png`, `events_de_timepicker.png`, `create-challenge_de_step_review.png`, `reports_incentivisation_de_data.png` |
| VB-351 | `reports_employee_de_columnpicker.png`, `reports_league_de.png`, `reports_league_en.png`, `reports_wellnessscore_de.png` |
| VB-352 | `contentlibrary_es_coldload_filters_english.png`, `contentlibrary_en_baseline.png` |
| VB-353 | `ar_rtl_not_implemented_overview.png` |
| VB-354 | — (localStorage test, no visual defect) |
| VB-355 | `announcement_de.png`, `announcement_de_create.png`, `createcontent_de_typepicker.png`, `createcontent_de_bitesize_builder.png`, `emaildesigner_de.png`, `wellnessscore_de.png`, `overview_de.png`, `overview_en_baseline.png` |
| VB-356 | `create-challenge_de_landing.png`, `create-challenge_es_landing.png`, `create-challenge_en_landing.png`, `events_de_audience_country.png`, `contentlibrary_de.png`, `dynflow_announcement_de_deletedialog.png`, `addemployees_de.png` |
| VB-357 | `ar_rtl_not_implemented_overview.png` |
| VB-358 | `de_uploadpoints_preview_accumulates.png`, `settings_de_teamsize_validation.png`, `dynflow_uploadpoints_de.png`, `settings_de_save_toast.png` |
| VB-359 | — (dictionary analysis) |
| VB-360 | `settings_de_teamsize_tooltip.png`, `contentlibrary_de.png` |
| VB-361 | `healthinsights_de_blocked.png`, `create-challenge_de_step4_config.png` |

## Tracking JQL

```
labels = i18n AND labels = fit-admin ORDER BY priority DESC
labels = i18n-rootcause AND status != Done
labels in (i18n-ac1-switch, i18n-ac2-strings, i18n-ac4-layout, i18n-ac5-persist) AND status != Done
```

Note `VB-360` deliberately carries `accessibility` + `fit-admin` but **not** `i18n`, so it stays out of the
i18n queries and doesn't inflate the localization bug count.
