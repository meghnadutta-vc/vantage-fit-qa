# Vantage Fit Admin — String Inventory (Frontend vs Backend)

**Phase 2 of 3** (map → **classify strings** → test per language). Built from `DASHBOARD-MAP.md`,
the frontend i18n files, JS bundles, and API responses. Purpose: separate the strings the
**frontend** owns (Phase-3 test targets) from **backend** data (rendered verbatim; not our localization).

## How each string was classified (evidence-based)
1. **In `/assets/i18n/fit/en.json`?** → **Frontend (externalised)**. The de value tells us if it's
   translated. Full list: `string-inventory/fe-keys.md`.
2. **Not an i18n key, but a literal in a JS bundle / DOM chrome?** → **Frontend (hardcoded, not
   externalised)** — a FE gap that needs a key added.
3. **Appears as a value in an API response body?** → **Backend** (server-sent; leave for the
   backend translation ticket).

## Headline numbers
- **Frontend, externalised: 982 strings** (in en.json). **955 translated to German, 0 missing.**
  The **27 not translated are identical-by-design** loanwords/proper nouns (Name, OK, Team, Video,
  Podcast, Challenge, Community, Event, Bronze, Gold, E-Marathon, KM, System, Wellness, "https://",
  an email placeholder) — correct as-is.
- **Frontend, hardcoded (NOT externalised): a finite set** (below) — English literals with no i18n
  key. These are the real "whole screen in English" bugs (#15 Announcements, #16 Email Designer, #22
  upload text, #4 language names, #8 FREE).
- **Backend (API-served): a finite set** (below) — expected English until the backend is translated.

**Key takeaway:** the frontend translation work is essentially **done** (982 keys, ~99% translated).
The localization bugs are almost all **(a) wire-up** — a component renders an English literal
instead of the key whose translation already exists — or **(b) not-externalised** hardcoded strings.
Very little is an actual "missing translation."

---

## Category A — Frontend, externalised (982 keys) → Phase-3 targets
Every key should render its de/es/fr/pl value on screen. Grouped by namespace (≈ feature/module);
full list with en/de values in **`string-inventory/fe-keys.md`**.

| Module | i18n namespaces (key counts) |
|---|---|
| **Global chrome** | `common` (61), `fitMenu` (39), `fitActions` (15), `subheader` (16), `contactAmModal` (10), `fitInfoCard` (4), `fitSplash` (1), `fitOutlineButton` (1), `dataTable` (12), `qna` (4) |
| **Overview** | `overview` (24), `scoreBreakdown` (1), `teamScoreBreakdown` (1), `weeklyScoreItem` (1) |
| **Challenges** | `manageChallenge` (31), `contest` (38), `taskConfig` (27), `habitsModal` (18), `customChallengeConfig` (13), `customActivity` (12), `challengeWizard` (11), `staticChallenges` (10), `challengeReview` (9), `challengeDetails` (8), `journeyConfig` (8), `journeyInfo` (8), `createChallenge` (7), `participants` (7), `challengeSetup` (4), `leaderboardWidget` (3), `challengePosters` (2), `durationForm` (2) |
| **Programs / Content** | `contentLibrary` (20), `articleForm` (17) |
| **Community / Events** | `eventForm` (27), `events` (24) |
| **Announcements** | `announcementPage` (65) — *note: many keys exist, but the list-page chrome is hardcoded (see Category B)* |
| **Communications** | `sendCustomEmail` (23), `publishNotifications` (12), `previewEmails` (12), `notifPreview` (5) |
| **Workforce Health** | `wellnessScore` (38), `wellnessLeagues` (13), `wellnessBadges` (7), `wellnessScoreReport` (5), `leagueSteps` (2) |
| **Reports** | `reportCols` (37), `reportBuilder` (26), `reportFilter` (13), `reports` (10) |
| **Rewards** | `pointsUpload` (25), `bulkUpload` (8), `rewardForm` (7) |
| **Configuration** | `settings` (44), `addEmployees` (27), `settingsFlags` (14), `participantBulkUpload` (6) |
| **Shared (audience/teams/dialogs)** | `targetAudience` (43), `privacyForm` (21), `team` (15), `inviteUserDialog` (3), `addMemberModal` (2), `selectedEmployeeModal` (1) |

### A.1 — Externalised but rendered in English anyway (WIRE-UP bugs) — highest-value FE fixes
Translation exists in de.json but the component shows the English literal:
| Bug | String | i18n key (de value exists) |
|---|---|---|
| #1 | "All Countries" | `targetAudience.filtersAll.country` ("Alle Länder") |
| #2 | "This Month" | `subheader.presets.this_month` ("Dieser Monat") |
| #6 | "Custom/Race/Journey/Streak Challenge" + descriptions | `staticChallenges.*.title/description` |
| #8 | "NEW" badge | `common.newTag` ("NEU") |
| #12 | "What would you like to create?" chooser | `fitActions.title` |
| #14 | content type "Article" | `contentLibrary.types.article` ("Artikel") |
| #18/#20 | report column-picker names ("Employee ID"…) | `reportCols.*` |
| #28 | delete dialog "You won't be able to revert this!" | `announcementPage.deleteText` |

### A.2 — Identical-by-design (27) — NOT bugs
`common.name/ok/team`, `contentLibrary.types.{video,podcast}` + `stats.{videos,podcasts}`,
`contest.{tabChallenge,tabTeams}`, `fitActions.{challenge,event}.title`, `fitMenu.community` +
`groups.challenges`, `fitInfoCard.challenges`, `reportBuilder.{bronze,gold}`, `reportCols.name`,
`staticChallenges.e-marathon-challenge.title`, `targetAudience.sourceWellness`, `taskUnits.km`,
`wellnessBadges.system`, `contactAmModal.{gmail,outlook,nameLabel}`, `announcementPage.dialog.ok`,
`articleForm.urlPlaceholder` ("https://"), `team.membersEmailPlaceholder`. (Loanwords/proper nouns.)

---

## Category B — Frontend, hardcoded (NOT externalised) → needs an i18n key + translation
English literals in the JS/DOM with **no** i18n key (so they stay English in *every* language):
| # | Strings | Screen |
|---|---|---|
| #15 | "Announcements", "Write and publish announcements to your organisation.", "What is an Announcement?", "Existing Announcements", "Search by title...", "Delete announcement", "Show more", header "Create Announcement", "Publish" | Announcements list/form |
| #16 | "Rich Email Composer", "Send updates people actually open.", "People-first email", "Continue last email", "Start new", step names (Intro/Write/Design/Send), "Get started", "Import template" | Email Designer |
| #22 | "Click to upload or drag and drop" | Add Employees dropzone (Upload Points has the *keyed* version) |
| #4 | language option names ("English", "German", "Chinese (Simplified)"…) | Language switcher (shared component) |
| #8 | "FREE" badge | Content Library nav (NEW is keyed; FREE is not) |

*(Verification: these returned "not a frontend key" against en.json; the confirmed FE ones were also
found as literals in JS bundles.)*

---

## Category C — Backend (API-served, verbatim) → route to backend ticket, NOT Phase-3 FE
Confirmed present in API response bodies and absent from en.json:
| Strings | Screen | API source |
|---|---|---|
| Challenge status ("Ended", "Ends in N days", "Coming Soon", "Active") | Active/Past Challenges | `ongoing/upcomingCampaigns` → `statusString` |
| Challenge type ("Multi Week Multi Activity", "Race", "Journey") | challenge cards | `challengeTypeName` |
| Challenge names, pre-built template names + descriptions | Create/Active/Past Challenges | campaigns/templates data |
| Content titles + category values ("Mindfulness", "Exercise") | Content Library | content APIs |
| Wellness Score stat cards, chart titles, legends, segment names | Wellness Score | `…/wellness-score/insights/stream` → `header`/`label` |
| "Employee Wellness Scores" / "Individual employee wellness score details" | Wellness Score Report | `…/wellness-score/employee-report` → `title`/`subtitle` |
| 9 email-type titles + descriptions | Preview Emails | `…/email/getAll` → `title`/`description` |
| Plan name "Active Plan - Grow" | left-rail badge | `…/dashboard/config` → `plan.name` |
| Country / City lists | filters | country/city APIs |
| Report table data (names, emails, dates…) | Reports | report APIs |
| Announcement row titles ("Test Announcement") | Announcements | announcements data |
| Leadership Insights text; Ask Vantage Fit answers | Overview | AI / embedded widget |

---

## What this sets up for Phase 3
- **Test Category A per screen/language:** confirm each rendered string shows the de/es/fr/pl value
  (catches wire-up bugs A.1) — and test the **functional flow** of each control (e.g. filter click).
- **Category B:** verify they're still English in every language (language-agnostic FE gaps).
- **Category C:** out of scope for FE localization; note if any newly appear.
- Full FE key list to check against: `string-inventory/fe-keys.md`; raw data: `.playwright-mcp/fe_strings.json` (committed copy in `string-inventory/`).
