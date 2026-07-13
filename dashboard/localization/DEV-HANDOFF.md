# Dev Handoff — Vantage Fit Admin Localization (VF-2207) — grouped by root cause

**Verified 2026-07-13** against the frontend i18n files `/assets/i18n/fit/en.json` and
`/assets/i18n/fit/de.json` (982 keys each). Full evidence in `bug-logs/bug-log.md`.

**Headline:** the German translations are **largely done and correct in `de.json`**. Most bugs are
**not missing translations** — they are screens rendering **hardcoded English literals** (or the
wrong key) instead of the i18n value that already exists. So the dev fix is mostly *wiring*, not
*translating*.

---

## Bucket A — Translation EXISTS in de.json; component not using the key → **wire up / stop hardcoding**
For each: the key is present and correctly translated; the screen shows English anyway.

| Bug | Screen | i18n key | de.json value (already there) |
|---|---|---|---|
| #1 | Overview country filter | `targetAudience.filtersAll.country` | "Alle Länder" |
| #1/#19 | Filters (Notif/Reports/Leagues) | `targetAudience.filtersAll.{department,gender,ageGroup}`, `reportBuilder.allCountries` | "Alle Abteilungen", "Alle Geschlechter", "Alle Altersgruppen" |
| #2 | Overview date preset | `subheader.presets.this_month` | "Dieser Monat" |
| #5 | Left rail plan badge | `fitMenu.activePlan` | "Aktiver Plan" ("Grow" = plan tier, backend/brand) |
| #6 | Create Challenge type cards | `staticChallenges.{custom,race,journey,e-marathon,streak}-challenge.{title,description}` | "Individuelle Challenge", "Rennen-Challenge", "Selbst gestalten: Konfigurieren Sie jede Aufgabe…" |
| #8 (NEW) | "NEW" badge | `common.newTag` | "NEU" |
| #12 | Create-content chooser | `fitActions.title` | "Was möchten Sie erstellen?" |
| #14 | Content Library "Typ" column | `contentLibrary.types.article` (+ video/podcast) | "Artikel" |
| #18/#20 | Report/League column pickers | `reportCols.employeeId` etc. | "Mitarbeiter-ID" (headers already use these; pickers don't) |
| #28 | Delete confirmation dialog | `announcementPage.deleteText` | "Dies kann nicht rückgängig gemacht werden!" |

**Action:** point these components at the existing i18n keys (remove hardcoded literals). No new translation needed.

## Bucket B — No i18n key at all → **externalize + translate**
These English strings are hardcoded in components and absent from both en.json and de.json.

| Bug | Screen | String(s) |
|---|---|---|
| #15 | Announcements (whole page chrome) | "Announcements", "Write and publish announcements to your organisation.", "Existing Announcements", "Search by title...", "Delete announcement", "Show more", header "Create Announcement", "Publish" |
| #16 | Email Designer | "Rich Email Composer", "Send updates people actually open.", step names, "Get started", "Import template" |
| #22 | Add Employees upload box | "Click to upload or drag and drop" |
| #8 (FREE) | "FREE" badge (Content Library) | "FREE" |
| #4 | Language dropdown options | "English", "German", "Chinese (Simplified)", … (not in fit i18n — likely a shared language list) |

**Action:** add i18n keys for these and provide translations.

## Bucket C — Translation-quality bug in the de.json value itself → **fix the German string**
| Bug | Issue |
|---|---|
| #7 | Capitalization: "…eigenen **Neuen** Challenges" — adjective should be lowercase "neuen" (same in es "Nuevos", fr "Nouveaux") |
| #25 | Register: Spanish (es.json) uses informal **"tú"**; German/French use formal **Sie/vous**. Align on a formality per style guide |

## Bucket D — Frontend behaviour / formatting (not i18n strings)
| Bug | Issue |
|---|---|
| #3 / #11 | Dates render "Jul 01, 2026" — use locale-aware formatting (de → "01.07.2026") |
| #24 | `<html lang>` stays `"en"` in German mode — set it from the active locale (accessibility) |
| #26 | Two create choosers, one localized (global, German) vs one not (content, English #12) |
| #27 | Publish Notifications: Send enables with only a title (default audience = all) — accidental-send risk |
| #29 | Create Announcement: "Publish" never enables with valid Titel+Beschreibung — needs dev repro |

## Bucket E — Backend (NOT frontend; route to the backend translation ticket)
Rendered verbatim from API responses; German won't appear until backend is translated.

| Bug | Source (API) | Example |
|---|---|---|
| #9 | ongoing/upcomingCampaigns `statusString` | "Ended", "Ends in X days", "Coming Soon" |
| #10 | campaigns `challengeTypeName` | "Multi Week Multi Activity", "Race", "Journey" |
| #17 | workforce-health/wellness-score/insights/stream `header`/`label` | "Current Score", "Wellness Score by Department" |
| #21 | wellness-score/employee-report `title`/`subtitle` | "Employee Wellness Scores" |
| #23 | email/getAll `title`/`description` | "Welcome Email (Add Employee)" |
| #18? | (unresolved) | "Based on avg daily steps over 21 days" — not an i18n key, not in checked APIs; dev to confirm source |

---

## How this was verified (so anyone can re-check)
For any English string seen in the UI while German is selected:
1. Fetch `/assets/i18n/fit/en.json` + `de.json`, flatten to key→value.
2. Look it up by value: **in en.json** → frontend-owned (Bucket A if de has a real translation, C if de==en, B-adjacent if key absent). **Not a key + in a JS bundle** → hardcoded frontend literal (Bucket B). **In an API response body** → backend (Bucket E).
