# Jira tickets filed — Fit Admin Dashboard localization

Filed 2026-07-29 into project **BUGS (`VB`)**. Parent testing ticket: **VF-2207** (project Vantage Fit,
"[VF_Testing] Localisation on admin Fit — language switch, string coverage, fallback").

All 13 are linked to VF-2207 with **"relates to"**. All **unassigned** by design.

| Ticket | Type | Pri | Title | Source file(s) | FE / BE |
|---|---|---|---|---|---|
| [VB-349](https://vantagecirclejira.atlassian.net/browse/VB-349) | Bug | High | Text clipping and spill — 4 zero-headroom containers break at every width | `03-UI-LAYOUT.md` | **FE** |
| [VB-350](https://vantagecirclejira.atlassian.net/browse/VB-350) | Bug | High | Locale-unaware date/time formatter — English months in all 18 languages | `04-LOCALE-FORMATTING.md` | **FE** |
| [VB-351](https://vantagecirclejira.atlassian.net/browse/VB-351) | Bug | High | Shared report filter + column-selector renders hardcoded English defaults | `02`, `01`, `03` | **FE** |
| [VB-352](https://vantagecirclejira.atlassian.net/browse/VB-352) | Bug | High | Language not applied on cold load or after in-place switch | `06`, `01` | **FE** |
| [VB-353](https://vantagecirclejira.atlassian.net/browse/VB-353) | Bug | High | Arabic renders left-to-right — RTL not implemented | `01`, `03`, `07` | **FE** |
| [VB-354](https://vantagecirclejira.atlassian.net/browse/VB-354) | Bug | High | Language preference client-side only — not persisted server-side | `06`, `01` | **FE bug, needs BE work** |
| [VB-355](https://vantagecirclejira.atlassian.net/browse/VB-355) | Bug | High | Modules with no or unwired i18n keys — render in English | `02`, `01` | **FE** |
| [VB-356](https://vantagecirclejira.atlassian.net/browse/VB-356) | Bug | Medium | Individual wire-up gaps — translation exists but is not rendered | `02-UNTRANSLATED.md` | **FE** |
| [VB-357](https://vantagecirclejira.atlassian.net/browse/VB-357) | Bug | Medium | Numeral system and currency formatting incorrect per locale | `04-LOCALE-FORMATTING.md` | **FE** + 1 `[FE-BE TBD]` |
| [VB-358](https://vantagecirclejira.atlassian.net/browse/VB-358) | Bug | High | Silent write failures + diacritic-blind search | `06-FUNCTIONAL.md` | **FE** (BE is correct) |
| [VB-359](https://vantagecirclejira.atlassian.net/browse/VB-359) | Task | Medium | No product-wide register/tone policy | `05-LINGUISTIC-QUALITY.md` | **Content/product** |
| [VB-360](https://vantagecirclejira.atlassian.net/browse/VB-360) | Bug | Medium | Accessibility defects (route to a11y epic) | `07-ACCESSIBILITY.md` | **FE** |
| [VB-361](https://vantagecirclejira.atlassian.net/browse/VB-361) | Task | Medium | QA follow-up — blocked coverage, source triage, decisions | `10`, `09`, `11`, `08` | **Triage** |

## Blocking dependency (encoded in Jira)

**VB-349 blocks VB-351 and VB-356.** The audience operator box (50px) and Wellness Leagues chips (110px) fit
today *only because their text is still untranslated English*. Ship the wire-up fixes first and the text
grows and clips across six report surfaces.

## Files NOT filed as tickets — attached to VF-2207 as evidence instead

| File | Why not a ticket |
|---|---|
| `00-INDEX.md` | Report front door |
| `01-P1-P2-CRITICAL.md` | **Priority view** — all 19 bugs deliberately repeat from 02–07. Filing it would double-file them |
| `09-NOT-A-BUG.md` | Cleared items — **attach so nobody re-files the ~14** |
| `11-AC3-FALLBACK.md` | AC3 test evidence (mostly PASS) |

## Screenshots — attach manually

The Jira MCP connector exposes issue creation, editing, comments and links, but **no attachment upload**.
Every ticket lists its exact screenshot filenames under an "Evidence" heading; drag them in from
`Localization/dashboard/evidence/`.

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
