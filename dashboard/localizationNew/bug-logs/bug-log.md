# Vantage Fit — Dashboard Localization — Master Bug Log

**Engagement:** Vantage Fit admin dashboard frontend localization · India tenant (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages:** German (deep) · English (baseline) · French + Spanish (dict-parity 991/991 complete + spot-checks — all bugs reproduce in fr/es)
**Compiled:** 2026-07-22 · Every bug is logged in full below. Per-module detail also lives in `bug-logs/<module>.md`.

> **Tags:** **[FE]** frontend (wire-up: translation exists but not rendered / wrong key; OR not-externalised: hardcoded, no key;
> OR locale-formatting/layout). **[BE]** backend/data-driven (from API/master-list/template; expected English until backend
> localization). **[FE-BE TBD]** source unproven — needs triage / product confirmation.

## Priority summary
| Priority | Count | IDs |
|---|---|---|
| **P1** | 0 | — (all three P1 leads G4/G5/G6 executed Run 11; none is a P1 — see `p1-hunt-g5-g6-g4.md`) |
| **P2** | 17 | OV#1, OV#2, **OV#12** · CC#1 · RPT#1, RPT#2 · CL#1 · EV#1 · CRC#1, CRC#2 · ANN#1, ANN#2 · ED#1 · WS#1 · **UP#4, UP#5** · **ES#1** |
| **P3** | 19 (+ Run 5–13 addendum IDs; see ADDENDUM) | OV#3, OV#5, OV#6, OV#7 · CC#2, CC#3, CC#4, CC#5 · MGC#1, MGC#2 · RPT#3, RPT#4, RPT#5 · CL#2, CL#3 · EV#2 · ANN#3 · SCE#1 · WL#1 · AE#1, AE#2 · UP#1, UP#2 · PE#1 · DF#1 · FR#1 · OV#4(a11y, cross-module) |
| **P4** | 4 | SET#1, SET#2 · CL#4, CL#5 |
| Blocked | 1 | Health Insights (external iframe) |

**⚠️ The "clean modules" claim below is superseded.** A corrected layout sweep (Runs 5+) found breakage in
15 of 17 modules, including **Settings and Publish Notifications** — both previously signed off CLEAN. Those
sign-offs were about *translation quality* (genuinely correct); the modules were simply never measured for
layout with a working detector. See the **ADDENDUM** at the end of this file for all Run 5–12 bugs
(+20 IDs across 6 languages).

---

# Overview  (`/fit/overview`)

### OV#1 — Overview main content not translated in de/fr/es · P2 · [FE]
```
[Localization - P2]  [Overview — main content area]
With language = German/French/Spanish, the sidebar localizes but almost all main content stays English:
top stat cards, Org Wellness Score card, Score Breakdown, "At a Glance" strip, "Recommended Actions"
(all 10 items), Workforce Health Snapshot, Wellness Tiers, Active Challenges header.
Expected: all static labels localize (overview.* and related keys exist in de/fr/es.json).
Actual: English. Wire-up-confirmed (translation exists): "Score Breakdown", "Workforce Health Snapshot",
"View Insights", "Health Status", "Top Deficiencies", "Wellness Tiers"/"Consistency based employee tiers",
"Active Challenges", "View all". Not-externalised (no key): stat-card labels (Enrolled Users, Active Users,
Incentivization, Participation Rate), "Across all countries and demographics", "Org Wellness Score",
"At a Glance", "Avg Steps/Active Minutes/Mindful Minutes/Avg Sleep", "/day", "Recommended Actions" + items,
"Aggregated insights only", "Based on avg daily steps over 21 days".
Technical: component renders literals / wrong keys; split not-externalised sub-strings during triage.
Evidence: evidence/overview_de.png, overview_fr.png, overview_es.png vs overview_en_baseline.png
```

### OV#2 — Country filter default "All Countries" never translated · P2 · [FE]
```
[Localization - P2]  [Overview — top filter bar]
Default country-filter label stays "All Countries" in de/fr/es.
Expected: "Alle Länder" / "Tous les pays" / "Todos los países".  Actual: "All Countries".
Technical: key targetAudience.filtersAll.country present in all langs; filter not consuming it. Wire-up.
Evidence: evidence/overview_de.png, overview_fr.png, overview_es.png
```

### OV#3 — Inconsistent localization within the screen · P3 · [FE]
```
[Localization - P3]  [Overview — stat cards / Org Wellness Score card]
Equivalent controls localized inconsistently: "View More" on Org Wellness Score stays English while the
same action on other cards localizes ("Mehr anzeigen/Voir plus/Ver más"); "vs Prev period" English vs
translated "vs Prev Quarter".
Expected: identical labels use one language/wording.  Actual: mixed.
Technical: two different strings — one wired to i18n, one hardcoded. Consolidate onto the keys.
Evidence: evidence/overview_de.png
```

### OV#4 — a11y: `<html lang>` and icon aria-labels don't reflect language · P3 · [FE]  (CROSS-MODULE)
```
[Accessibility - P3]  [document root + header/sidebar icon buttons — every page]
After switching to de/fr/es, document.documentElement.lang stays "en"; icon-button aria-labels
("Collapse sidebar", "Open profile menu") stay English.
Expected: <html lang> = de/fr/es; aria-labels localized.  Actual: lang="en"; aria-labels English.
Technical: bind <html lang> to active language; route aria-labels through i18n. Reproduces on ALL modules.
Evidence: runtime — document.documentElement.lang returns "en" while localStorage.fit_lang = de/fr/es
```

### OV#5 — Date range not locale-formatted · P3 · [FE]
```
[Localization - P3]  [Overview — date-range filter]
Date range renders US format regardless of language.
Expected: de "21. Juni 2026 – 20. Juli 2026", fr/es equivalents.  Actual: "Jun 21, 2026 - Jul 20, 2026".
Technical: no locale-aware date formatter. Same root as RPT#4, CC#5. ("Ended on Mar 26" in cards = backend.)
Evidence: evidence/overview_de.png
```

### OV#6 — Numbers/percentages/currency not locale-formatted · P3 · [FE-BE TBD]
```
[Localization - P3 / Needs Verification]  [Overview — Wellness Tiers %, Incentivization]
"23.7%/33.2%/53.1%" use dot decimal; Incentivization "$0" unchanged across languages.
Expected: locale formatting (de/fr/es "23,7 %"); locale currency per tenant rules.  Actual: unchanged.
Technical: some values from overview/home/stream — confirm FE formatting vs API. Currency choice may be an
intentional tenant setting (product confirm).
Evidence: evidence/overview_de.png
```

### OV#7 — Language change not applied until reload (stale strings) · P3 · [FE]
```
[Localization - P3]  [Overview + cross-module (Create Challenge builder)]
Switching language in-place updates sidebar but leaves some already-rendered strings stale until the route
reloads (e.g. date preset "Last 30 Days" stayed EN in-place, rendered "Letzte 30 Tage" on fresh load).
Expected: selecting a language re-translates the current view immediately.  Actual: some strings need reload.
Technical: OnPush/non-reactive binding — components read the translation once, don't subscribe to changes.
NOTE: all other bugs were re-verified on FRESH loads and still reproduce.
```

---

# Create Challenge  (`/fit/create-challenge` + Custom builder)

### CC#1 — 5 challenge-type cards not translated · P2 · [FE]
```
[Localization - P2]  [Create Challenge landing — "Create your own" cards]
Titles + descriptions of Custom/Race/Journey/E-Marathon/Streak stay English in de/fr/es (chrome localizes).
Expected: de "Individuelle Challenge"/"Rennen-Challenge"; es/fr equivalents.  Actual: English.
Technical: staticChallenges.*.title/description exist with full translations; card renders literal. Wire-up.
Evidence: evidence/create-challenge_de_landing.png, _fr_landing.png, _es_landing.png
```

### CC#2 — Date-picker calendar not localized · P3 · [FE]  (shared calendar)
```
[Localization - P3]  [Create Challenge → Custom builder → Set Duration date picker]
Calendar shows English month header ("JUL 2026"), weekday names/initials, and "Close calendar" while the
page is German.  Expected: localized month/weekday names + control labels.  Actual: English.
Technical: date-picker not receiving a locale. SAME calendar as Reports (RPT#4) and Events.
Evidence: evidence/create-challenge_de_datepicker.png
```

### CC#3 — Audience operator "is in" not translated · P3 · [FE]  (shared audience widget)
```
[Localization - P3]  [Create Challenge → audience step]
Filter operator "is in" (Department/Country/Gender/Age group) stays English.
Expected: de "ist in".  Actual: "is in" ×4.
Technical: query-builder operator not wired to i18n. Same widget as Events EV#1. Filter VALUES = data.
Evidence: evidence/create-challenge_de_step3_privacy.png
```

### CC#4 — Activity/task-type names not translated · P3 · [FE-BE TBD]
```
[Localization - P3 / Needs FE-BE triage]  [Create Challenge → Config step]
~24 activity names (Steps, Water Intake, Yoga Session, …) English in German mode while step chrome localizes.
Expected: localized (or product decision they are fixed).  Actual: all English.
Technical: likely a backend activities master list — if backend, expected EN until backend phase; if i18n keys
exist, FE wire-up. Triage before assigning owner.
Evidence: evidence/create-challenge_de_step4_config.png
```

### CC#5 — Review/detail date values, "Week n", "Custom Image" not localized · P3 · [FE]
```
[Localization - P3]  [Create Challenge → Review / published campaign detail]
Date values render English month names ("22 July 2026"); "Week 1/2/3" English on Review + detail (Config uses
"Woche n"); template flow adds a logo option labelled "Custom Image".
Expected: de dates + "Woche n" everywhere + localized "Custom Image".  Actual: as above.
Technical: non-locale date formatter (same as OV#5); "Week n" hardcoded literal on Review/detail; "Custom
Image" not externalised.
Evidence: evidence/create-challenge_de_step_review.png, _de_published_detail.png, _de_template_prefilled.png
```

---

# Manage Challenges  (`/fit/manage-challenge`)

### MGC#1 — Card countdown "Ends In X Days" not translated · P3 · [FE-BE TBD]
```
[Localization - P3 / Needs FE-BE triage]  [Manage Challenges — listing cards]
Each card's status "Ends In 27 Days" stays English while rest of card localizes.
Expected: de "Endet in 27 Tagen".  Actual: "Ends In X Days".
Technical: no i18n key found; likely a backend statusString OR FE literal. (Section keys
manageChallenge.statusOngoing/Upcoming/Completed DO exist & localize — the per-card countdown doesn't use them.)
Evidence: evidence/manage-challenge_de.png vs manage-challenge_en.png
```

### MGC#2 — Chatbot overlay blocks "Update Challenge" button · P3 · [FE — non-localization]
```
[UI / Functional - P3]  [Manage Challenges → Edit]
"Ask Vantage Fit" floating widget overlaps the "Update Challenge" CTA and intercepts the click (programmatic
click succeeds → button works, but is interaction-blocked at this viewport).
Expected: CTA clickable; widget doesn't overlap primary actions.  Actual: overlay intercepts pointer events.
Technical: chatbot FAB/panel z-index/hit-area overlaps the form footer. Found incidentally; not a loc defect.
Evidence: evidence/manage-challenge_de_edit.png
```

---

# Reports (all 6)  (League/Employee/Participation/Incentivisation/Wellness Score/Redemption)

### RPT#1 — Report filter defaults not translated · P2 · [FE]  (shared filter bar)
```
[Localization - P2]  [Reports — top filter bar, all 6]
Filter chips "All Countries/Departments/Genders/Age Groups", "Enrolled", "Active Users" stay English while
date preset + table headers localize.
Expected: de "Alle Länder/Abteilungen/Geschlechter/Altersgruppen", "Registriert", "Aktive Benutzer".
Technical: same class as OV#2; report filter bar doesn't consume targetAudience.filtersAll.* etc. ALSO hits
Wellness Score + Wellness Leagues.
Evidence: evidence/reports_league_de.png, reports_employee_de.png, reports_wellnessscore_de.png
```

### RPT#2 — Column-selector control fully untranslated · P2 · [FE]  (shared control)
```
[Localization - P2]  [Reports — column multiselect, all with picker]
Button ("Date of Joining(+5 others)"), option names, "N selected", "You have selected all the options",
"All" all English — while the table headers they control ARE localized (Eintrittsdatum, Abteilung, Land…).
Expected: localized options + control text.  Actual: entire control English. ALSO on Wellness Leagues.
Technical: picker uses a separate string set than the header rendering; route both through the same keys.
Evidence: evidence/reports_employee_de_columnpicker.png
```

### RPT#3 — WSR "Employee Wellness Scores" section not translated · P3 · [FE]
```
[Localization - P3]  [Reports → Wellness Score Report]
Report title/subtitle localize, but section "Employee Wellness Scores" + "Individual employee wellness score
details" stay English.  Expected: localized.  Actual: English.  Technical: not externalised / not wired.
Evidence: evidence/reports_wellnessscore_de.png
```

### RPT#4 — Report date values not locale-formatted + inconsistent + English calendar · P3 · [FE]
```
[Localization - P3]  [Reports — filter value + table cells + date-range picker calendar]
In German, 3 different date formats appear (filter "Jan 01, 2026 - Jul 20, 2026" MMM D; Employee/Participation
cells "24-06-2026" DD-MM-YYYY; Incentivisation cells "2026-03-26" YYYY-MM-DD) — none German (DD.MM.YYYY). The
date-picker CALENDAR (weekdays "Su Mo Tu…", months "Jan/Feb…") is English while presets/Apply localize.
Expected: consistent locale dates + localized calendar.  Actual: as above.
Technical: 3 formatters + un-localized calendar (same calendar as CC#2). Standardise on one locale-aware adapter.
Evidence: evidence/reports_employee_de.png, reports_incentivisation_de_data.png
```

### RPT#5 — Currency values not locale-formatted · P3 · [FE-BE TBD]
```
[Localization - P3 / Needs Product Confirmation]  [Reports → Incentivisation (Wert); Redemption unverified]
Currency renders as code + integer ("INR 25", "USD 1", "GBP 1") — language-neutral but not locale-formatted.
Expected: locale currency (symbol/decimals/placement per product intent).  Actual: code+integer.
Technical: confirm whether code-prefix is intentional (mixed-currency) vs locale formatting required.
Thousands-grouping unverified (only small integers present).
Evidence: evidence/reports_incentivisation_de_data.png
```

---

# Configuration → Settings  (`/fit/configuration/settings`) — static CLEAN; 2 observations

### SET#1 — Language switcher lists options in English regardless of UI language · P4 · [FE-BE TBD]
```
[UX / Copy - P4 / Needs Product Confirmation]  [Sidebar footer — content-language dropdown (global)]
Dropdown options stay English ("English","German","Arabic","Chinese (Simplified)"…) while the surrounding UI
is German.  Expected (judgment): endonyms (Deutsch/Français/…) or a single reference language by design.
Actual: all English. Global element, all Fit pages. Confirm product intent.
Evidence: evidence/settings_de.png
```

### SET#2 — "Max team size" info icon has no accessible label · P4 · [FE]
```
[Accessibility - P4]  [Settings → Challenge-Einstellungen → Maximale Teamgröße info (ℹ)]
Icon has no aria-label; its text ("Min.: 5 · Max.: 500 Mitglieder pro Team") shows only on mouse hover →
unavailable to keyboard/SR users. (Tooltip text itself IS localized — a11y gap, not a loc defect.)
Evidence: evidence/settings_de_teamsize_tooltip.png
```

---

# Programs → Content Library  (`/fit/programs/on-demand-content`)

### CL#1 — Content-type labels English in Type filter + table "Typ" column · P2 · [FE]
```
[Localization / Functional - P2]  [Content Library — Type filter + table Typ column]
Type filter ("All/Article/Video/Podcast/Bite Size") and table Typ cells ("Article") render English though
contentLibrary.types.all="Alle", .article="Artikel", .bite_content="Häppchen" exist AND the Content Overview
panel renders "Artikel" (via contentLibrary.stats.*). de "Bite Size" should be "Häppchen".
Expected (de): "Alle/Artikel/Video/Podcast/Häppchen".  Actual: English (Video/Podcast identical both langs).
Technical: filter + table not consuming contentLibrary.types.* (summary does). Same class as OV#1.
Evidence: evidence/contentlibrary_de.png, contentlibrary_de_full.png
```

### CL#2 — Category filter trigger shows "All" while options localize · P3 · [FE]
```
[Localization - P3]  [Content Library — Category filter (trigger button)]
Options localize (first option "Alle" + category data) but the collapsed trigger BUTTON shows "All".
Expected: trigger reflects "Alle".  Actual: "All". Technical: trigger uses a hardcoded default label.
Evidence: evidence/contentlibrary_de.png
```

### CL#3 — Bite-Size "N language(s)" badge hardcoded English · P3 · [FE]
```
[Localization - P3]  [Content Library — Bite-Size rows, Typ cell]
Language-count badge stays English: "1 language", "2 languages", "7 languages".
Expected (de): "1 Sprache"/"N Sprachen".  Actual: English.
Technical: no matching plural i18n key — hardcoded literal; needs a key + plural handling.
Evidence: evidence/contentlibrary_de_full.png
```

### CL#4 — "Ask Vantage Fit" assistant widget English · P4 · [FE-BE TBD]  (global)
```
[Localization / UX - P4 / Observation]  [Global floating "Ask Vantage Fit" widget — all pages]
Widget ("Ask Vantage Fit anything") + rotating prompts stay English. May be intentionally English-only for
now — confirm scope. Logged once; applies cross-module.
Evidence: evidence/contentlibrary_de.png
```

### CL#5 — Action-column icon buttons have no accessible name · P4 · [FE]
```
[Accessibility - P4]  [Content Library — Aktionen column icon button]
Per-row icon button exposes an empty accessible name (button ""). a11y gap, not a loc defect.
Evidence: evidence/contentlibrary_de_full.png
```

---

# Programs → Create Content  (`?action=create` → Linked Content / Health Bite)

### CRC#1 — "Create content" type-picker modal hardcoded English · P2 · [FE]
```
[Localization - P2]  [Create Content — type-picker modal]
Modal entirely English: "Create content", "What would you like to create?", "Linked Content"/"Add an article,
video or podcast link.", "Health Bite"/"Author a bite-size content experience."
Expected: localized.  Actual: English. Technical: NOT externalised — no i18n keys (only fitMenu.createContent
="Inhalt erstellen" exists for the sidebar item). (The Linked Content FORM, by contrast, is fully localized.)
Evidence: evidence/createcontent_de_typepicker.png
```

### CRC#2 — Bite-Size Content Builder entirely English · P2 · [FE]
```
[Localization - P2]  [Create Content → Health Bite — /fit/create-bite-size-content]
Whole builder English: "Create Bite-Size Content", "Author short, snackable wellness content…", tabs
"Languages"/"Add Content", "Pick one or more languages…", "Next". Language checkboxes list EN names (SET#1).
Expected: localized.  Actual: English. Technical: NOT externalised — dictionary scan returns 0 keys. The
VF-2126 Bite-Size feature has no i18n support.
Evidence: evidence/createcontent_de_bitesize_builder.png
```
*Minor: the Content-Library row EDIT modal reuses the create title "Verknüpften Inhalt erstellen", and an
UPDATE fires the create toast "Inhalt erfolgreich erstellt" (should say updated). Copy nit — [FE].*

---

# Community → Events  (`/fit/events`, `/fit/events/create-event`)

### EV#1 — Target-audience dropdowns render English control strings · P2 · [FE]  (shared widget)
```
[Localization / Functional - P2]  [Create Event → Zielgruppe: Land/Stadt/Altersgruppe/Abteilung]
Audience dropdowns render "All", "All Countries" (and All Departments/Age Groups/…), "0 selected" in English
though targetAudience.filtersAll.country="Alle Länder", targetAudience.filters.country="Land" exist.
Expected: "Alle", "Alle Länder", "0 ausgewählt".  Actual: English.
Technical: shared multiselect not consuming targetAudience.* — same root/component as CC#3. Applies to all 4
audience fields. (Contrast: the attribute-style audience filter in Publish Notifications localizes.)
Evidence: evidence/events_de_audience_country.png
```

### EV#2 — Event time picker uses 12-hour AM/PM · P3 · [FE]
```
[Localization / Locale-format - P3]  [Create Event — Startzeit/Endzeit dropdowns]
Times listed as US 12-hour AM/PM ("12:00 AM", "1:00 AM" …). German convention is 24-hour; AM/PM not used.
Expected (de): 24-hour ("00:00", "13:30").  Actual: 12-hour AM/PM. Product-confirm; consistent with date gap.
Evidence: evidence/events_de_timepicker.png
```
*Cross-module reproduced here: event-card date values EN format; date-picker calendar weekdays "S M T W T F S"
EN (CC#2).*

---

# Community → Create Announcement  (`/fit/community/announcement`)

### ANN#1 — Landing/list view renders entirely in English · P2 · [FE]
```
[Localization - P2]  [Create Announcement — landing/list]
Whole landing English: "Announcements", "Write and publish announcements to your organisation.", "What is an
Announcement?" + body, "Existing Announcements", "Search by title...", column "Title", "Delete announcement",
"Show more" / "371 remaining".
Expected: fully localized.  Actual: 100% English. Technical: de.json HAS a complete announcementPage.* (~66
keys) + qna.announcement.* set; component renders literals instead. Wire-up (same class as OV#1).
Evidence: evidence/announcement_de.png
```

### ANN#2 — Create form partially localized (mixed language) · P2 · [FE]
```
[Localization - P2]  [Create Announcement — create form]
Localized: AI panel ("Mit KI generieren", prompt, "Ton: Geschäftlich", "Generieren"), "Titel*"+placeholder,
"Beschreibung*"+placeholder, "Text verbessern". English: heading "Create Announcement", subtitle, breadcrumb,
"Audience & Delivery" + subtitle, "Select City(s)"/"Select Country(s)"+"Select", "Publish" CTA.
Expected: fully localized.  Actual: mixed (primary CTA + audience English). Keys exist (announcementPage.title/
subtitle/audienceTitle="Zielgruppe & Zustellung"/publish="Veröffentlichen"). Wire-up.
Evidence: evidence/announcement_de_create.png
```

### ANN#3 — Delete dialog + delete toast + publish toast render English · P3 · [FE]  (dynamic)
```
[Localization - P3]  [Create Announcement — delete confirm dialog, delete toast, publish toast]
Delete confirm dialog: "Are you sure you want to delete?" / "You won't be able to revert this!" / "Cancel" /
"Delete". Delete toast: "Success" / "Announcement successfully deleted." Publish toast: "Success — Announcement
creation in progress ...". All English though announcementPage.deleteHeading="Möchten Sie wirklich löschen?",
.deleteText, .success="Erfolg", .deleteSuccess="Ankündigung erfolgreich gelöscht." exist.
Expected: localized dialog + toasts.  Actual: English. Technical: wire-up, same root as ANN#1/#2 — module's
dynamic strings unwired too. Verified on delete AND publish paths.
Evidence: evidence/dynflow_announcement_de_deletedialog.png
```

---

# Communications → Publish Notifications  (`/fit/community/publish-notifications`)
**CLEAN — 0 defects.** Chrome, content fields, audience tabs + attribute filters ("ist in", "Alle Abteilungen"),
send/load buttons, live preview all localize. Send success toast localized ("Benachrichtigung an 1 Benutzer
gesendet."). Confirms the audience translations work when wired (contrast EV#1/CC#3).

---

# Communications → Send Custom Email  (`/fit/community/send-custom-email`) — page CLEAN

### SCE#1 — Email template preview boilerplate English (mixed) · P3 · [FE-BE TBD]
```
[Localization / Copy - P3 / Needs Product Confirmation]  [Send Custom Email — E-Mail-Vorschau template iframe]
Template mixes languages: German injected placeholders ("Ihre Überschrift wird hier angezeigt") + English
boilerplate ("Hi {name},", "Open Vantage Fit", "If the button above does not work, copy and paste this link
into your browser:", "Warm Regards, Vantage Fit Team", "Download the Vantage Fit app").
Expected: consistent language.  Actual: mixed. Technical: email language may intentionally follow the
RECIPIENT locale / a template store (backend) rather than the admin dashboard language — confirm.
(Admin page chrome fully localized; send success toast localized "E-Mail an 1 Benutzer gesendet.")
Evidence: evidence/sendemail_de.png
```

---

# Communications → Email Designer  ("Rich Email Composer" dialog)

### ED#1 — Rich Email Composer entirely English · P2 · [FE]
```
[Localization - P2]  [Email Designer — Rich Email Composer dialog]
Entirely English: title "Rich Email Composer"; stepper "Intro/Write/Design/Send"; value prop ("PEOPLE-FIRST
EMAIL", "Send updates people actually open.", "Build a polished, on-brand email…", 3 points); actions
("Continue last email", "Start new", "Get started", "Import template"); "Start from a template" gallery
(Blank/Program Launch/Streak/Journey/Multi-Activity/Wellness Leagues/Health Insights/Redemption/Training Plans/
Winners & Spotlight) + badges.
Expected: localized.  Actual: English. Technical: NOT externalised — only fitMenu.emailDesigner + 2
sendCustomEmail launcher keys exist; composer strings have no keys. Same class as CRC#2.
Evidence: evidence/emaildesigner_de.png
```

---

# Analyze → Workforce Health

### Health Insights — BLOCKED · [BE / external]
```
[Blocked]  [Workforce Health → Health Insights (/fit/workforce-health/health-insights)]
Page body is an <iframe> to dash-vfit.vantagecircle.org → "refused to connect." External embedded analytics
app, not localizable within this engagement. Same blocker as the older engagement (#13).
Evidence: evidence/healthinsights_de_blocked.png
```

### WS#1 — Wellness Score analytics largely English (mixed) · P2 · [FE]
```
[Localization - P2]  [Workforce Health → Wellness Score (/fit/workforce-health/wellness-score)]
German: heading/subtitle, component weights ("Gesundheit: 20 % Gewichtung"…), "N Regionen", "N Mitarbeitende",
"Einblicke", "KI-generiert", "Nur für HR-Admins", empty states. English: stat cards ("Current Score",
"12-Month Average", "Industry Benchmark" + "-31 vs Industry"/"below benchmark"/descriptions); chart titles +
subtitles ("Org Wellness Score Trend", "How the Wellness Score is Composed", "Component Trends Over Time",
"Wellness Score by Department/Geography/Age Group/Gender", "Correlation: Challenges & Programs Impact"); legends
("High (>=80)/Moderate/Low"); correlation stats ("High Participation Users", "Program Adherents",…);
"Employee Wellness Scores". One mixed line: "Current period breakdown • Gesamtpunktzahl: 43".
Expected: fully localized.  Actual: mostly English analytics on a German frame.
Technical: de.json HAS wellnessScore.* (49 keys incl. sections.* German titles) — partly not-wired, partly
not-externalised (card subtitles/legends have no keys). Also inherits RPT#1 filter chips.
Evidence: evidence/wellnessscore_de.png
```

### WL#1 — Wellness Leagues tier-distribution subtitle English · P3 · [FE]
```
[Localization - P3]  [Workforce Health → Wellness Leagues — "Aktuelle Stufenverteilung" subtitle]
Card subtitle "Based on avg daily steps over 21 days" English while title + rest localize.
Expected: localized.  Actual: English. Technical: hardcoded/not-wired subtitle. Page also inherits RPT#1
(filter defaults) + RPT#2 (column selector "Employee ID(+8 others)").
Evidence: evidence/wellnessleagues_de.png
```

---

# Manage → Rewards → Upload Points  (`/fit/reward-hub/upload-points`) — static CLEAN

### UP#1 — CSV "Preview" modal title English · P3 · [FE]  (shared upload-preview modal)
```
[Localization - P3]  [Upload Points (and Add Employees) — CSV Preview modal]
The CSV Preview modal TITLE is "Preview" (English) while its trigger button is "Vorschau" (German). Same
shared component appears on Add Employees.  Expected: "Vorschau".  Actual: "Preview".
Evidence: evidence/dynflow_uploadpoints_de.png
```

### UP#2 — Upload success toast English · P3 · [FE]  (dynamic)
```
[Localization - P3]  [Upload Points — upload success toast]
After a valid CSV upload, toast reads "Success — File uploaded" in English.
Expected (de): localized ("Erfolg" / "Datei hochgeladen").  Actual: English. Wire-up/not-externalised.
Evidence: (dynamic; 1-pt test upload succeeded)
```

---

# Manage → Configuration → Add Employees  (`/fit/configuration/add-employees`)

### AE#1 — File-upload dropzone prompt English · P3 · [FE]
```
[Localization - P3]  [Add Employees — file-upload dropzone]
Dropzone reads "Click to upload or drag and drop" in English, while the IDENTICAL control on Upload Points is
German ("Zum Hochladen klicken oder ziehen und ablegen").  Expected: German.  Actual: English.
Technical: wire-up inconsistency (the German string exists and is used on Upload Points).
Evidence: evidence/addemployees_de.png
```

### AE#2 — Upload success toast English · P3 · [FE]  (dynamic)
```
[Localization - P3]  [Add Employees — upload success toast]
After a valid CSV upload, toast reads "The file was successfully uploaded. The processing time is typically 15
minutes, although this can vary." in English. Also the shared "Preview" modal title is English (UP#1).
Expected (de): localized.  Actual: English. Wire-up/not-externalised.
Evidence: (dynamic; test employee "QA Test Account" accepted)
```

---

# Manage → Configuration → Preview Emails  (`/fit/configuration/preview-emails`) — chrome CLEAN

### PE#1 — 9 email-type card titles + descriptions English · P3 · [FE-BE TBD]
```
[Localization - P3 / Needs FE-BE triage]  [Preview Emails — the 9 email-notification cards]
Page chrome German (whole previewEmails.* namespace consumed, incl. save toast "E-Mail-Einstellungen
erfolgreich gespeichert." which IS localized), but every email-type card title + description is English:
"Welcome Email (Add Employee)"/"Received when an employee is added…", "Welcome Email (Invite to Challenge)",
"Intro to App", "Challenge Reminder", "Challenge Start", "Weekly Summary", "Challenge Completion",
"Event Invite / RSVP Confirmation", "Direct Message from HR" (+ descriptions).
Expected: localized.  Actual: English. Technical: NO previewEmails.* keys for these → hardcoded FE literals OR
backend email-template metadata (triage; if backend, backend-localization item).
Evidence: evidence/previewemails_de.png
```

---

# Cross-cutting

### DF#1 — Generic request/loading toast English · P3 · [FE]  (global interceptor)
```
[Localization - P3]  [Global — request/loading toast (seen on Create Content, Add Employees)]
The shared request toast "This request is taking longer than expected. Please wait..." renders English on slow
requests. Likely a global HTTP-interceptor message → app-wide.
Expected: localized.  Actual: English. Contrast: most success toasts localize.
Evidence: (dynamic; observed during Create Content image processing + Add Employees upload)
```

---

# French & Spanish

### FR#1 — French label truncated in Settings size chip · P3 · [FE]
```
[UI / Localization - P3]  [Settings — banner/logo "recommended size" chips (French)]
With UI=French, the size chip clips the longer French label: "Taille de bannière recommandée" → "…recommand";
"Taille de logo recommandée" likewise. Spanish ("Tamaño de banner recomendado") and de/en fit.
Expected (fr): full label.  Actual: truncated. Technical: fixed-width overflow, only surfaces with longer
Romance-language text — recommend a truncation sweep of fixed-width chips/badges in fr.
Evidence: evidence/settings_fr_pass.png (compare settings_es_pass.png = clean)
```
- **Dictionary parity:** fr.json + es.json = 991/991 keys, 0 missing, 0 empty. So NO fr/es missing-translation
  bugs; all bugs above (language-agnostic wire-up / not-externalised) reproduce identically in fr + es.

---

# Backend / data-driven [BE] — expected English until backend localization (NOT frontend bugs)
- Challenge **status** ("Active"/"Not Started"/"Ended on <date>") and **type** ("Multi Week Multi Activity",
  Journey/Race/E-Marathon) — campaign/stream APIs.
- Deficiency names (Vitamin D, Sleep Quality, Stress Levels), health-status values (Normal, Needs Attention),
  tier names (Gold/Silver/Bronze), plan name ("Grow"), challenge/event/content **names**, report **cell data**
  (names, emails, department, country, product), Reports "Grund"/reason text — data/copy.
- Health Insights embedded analytics app (external).
- Candidates needing triage (may be BE): CC#4 activity master list, MGC#1 "Ends In X Days", RPT#5 currency,
  PE#1 email-type metadata, SCE#1 email template.

# Cross-cutting fix-once patterns
Report filter bar (RPT#1 → Wellness Score/Leagues) · column selector (RPT#2 → Wellness Leagues) ·
target-audience multiselect (EV#1 = CC#3) · date-picker calendar (CC#2 = RPT#4) · date/time formatters
(OV#5/RPT#4/CC#5/EV#2) · `<html lang>` (OV#4, every page) · Ask-VF widget (CL#4, every page) · generic loading
toast (DF#1, every slow request) · newer rich-builders shipped with no i18n (CRC#1/#2, ED#1).

---
---

# ADDENDUM — Runs 5–12 (2026-07-28)

Bugs found after the 2026-07-22 compilation above. Sources: `ui-break-sweep-de.md`, `ui-break-sweep-es.md`,
`spanish-full-sweep.md`, `desktop-1920-de-es-crud.md`, `p1-hunt-g5-g6-g4.md`, `multilang-fr-pt-pl-zh.md`.
**Languages now covered: de · es · fr · pt · pl · zh-CN** (+ en baseline).

> **Method note.** The pre-Run-5 sweeps used a detector that only caught `overflow:hidden` clipping, so the
> Truncation/Overlap ✅ ratings above are unreliable. Runs 5+ use `scrollWidth > clientWidth` with overflow
> classified as **CLIP** (cut off) / **SPILL** (collides) / **SCROLL** (`overflow:auto` data tables — NOT a
> defect and excluded from counts).

## Overview

### OV#8a — At-a-Glance metric labels overflow at ≤1440 (German only) · P3 · [FE]
[UI — Overview → At-a-Glance tiles, `.item-header` 113px]
German labels overflow a 113px box and render over the tile icon. English **and** Spanish both fit in
exactly 113px (0px headroom) — a zero-headroom container.
**Expected:** labels fit or truncate gracefully in every language.
**Actual:** de 3/4 overflow (+4 / +8 / +27). en/es fit exactly.
**Note:** does NOT reproduce at 1920. Distinct from OV#8b.

### OV#8b — Same tiles break in EVERY language incl. English at 1024 · P3 · [FE — responsive, not localization]
[UI — Overview → At-a-Glance tiles at 1024]
Container shrinks to 44px and all labels overflow regardless of language (en +5/+18/+18/+4).
**Expected:** tiles reflow at narrow widths.
**Actual:** every language overflows. **This is a responsive defect and must not be billed to localization.**
Separate ticket/owner from OV#8a.

### OV#9 — Stat-card headers overflow at 1024; Spanish worse than German · P3 · [FE]
[UI — Overview → stat cards]
de +69 / +63 / +35; **es +51 on the Incentivización card vs de +35** (*Incentivación* > *Anreize*).
**Note:** a German-only test under-reports this card — evidence that "German is longest" is unsafe.

### OV#12 — Workforce Health Snapshot / Wellness Tiers card is English in ALL SIX languages · P2 · [FE] wire-up
[Localization — Overview → Workforce Health Snapshot / Wellness Tiers card]
The whole card renders English in de/es/fr/pt/pl/zh-CN although complete translations exist for every
string: *Workforce Health Snapshot*, *Wellness Tiers*, *Consistency based employee tiers*, *View Insights*,
*Gold*, *Silver*, *Last 30 Days*.
e.g. `Wellness Tiers` → Paliers de bien-être (fr) / Níveis de bem-estar (pt) / Poziomy dobrostanu (pl) /
健康等级 (zh); `Gold` → Or / Ouro / Złoto / 黄金.
**Expected:** card renders in the selected language.
**Actual:** English in every language tested.
**Why highest-value:** ONE card, ~7 keys, and the fix closes a translation leak **and** a layout break in six
languages. The `.tiers-card` **+122px spill is identical in all six including Chinese** — where translated
strings shrank and break counts halved — proving the overflow is caused by the untranslated English, not by
text expansion.
**Supersedes:** **OV#10** ("Last 30 Days" subtitle) and **OV#11** (tier row +122px) — same unwired card.

### OV#10 — "Last 30 Days" subtitle English in all languages · P3 · [FE] → folded into OV#12
### OV#11 — Tier row overflows +122px identically in all languages · P3 · [FE] → folded into OV#12

## Create Challenge
### CC#6 — Pre-built template grid overflows +151px at 1024 in every language · P3 · [FE — responsive]
[UI — Create Challenge → `.pre-built-templates-wrapper` / template grid]
+151px in en/de/es/fr/pt/pl/zh alike → structural, not translation-driven.

### CC#1 (extended) — template titles AND descriptions English; buttons localized · P2 · [FE]
Full inventory: *Custom Challenge · Race Challenge · Journey Challenge · E-Marathon · Streak Challenge*, plus
all 5 descriptions, render English while the card buttons render correctly ("Crear desafío" / "Challenge
erstellen"). Mixed-language cards. Spanish values exist (*Desafío personalizado*, *Desafío de carrera*…).
Also leaks onto **Manage Challenges** (Race Challenge / E-Marathon) in fr/pt/pl/zh.

## Manage Challenges
### MGC#3 — Card action row overflows at 1024 · P3 · [FE]
de +62px / 97 cards · es +45px / 65 cards · en +30px / 65 cards.

### MGC#4 — 5 broken card images · P3 · [FE-BE TBD]
Identical count in every language and at every resolution → malformed CDN URLs, language-independent.

## Past Challenges
### PC#1 / PC#2 — card title clipped; 1 broken image · P3
`card-title` CLIP +7…+12px on long *content* titles (authored data, not UI strings). 1 broken image.

## Reports
### RPT#6 — Report tables overflow at 1024 · NOT A DEFECT (recorded to prevent re-flagging)
`.fit-table-scroll` +334 / +454 / +1002 are `overflow:auto` **scrollable** containers. Wide data tables are
meant to scroll horizontally.

### RPT#7 — Empty state instructs clicking a button that does not exist · P3 · [Copy]
[Copy — Employee Report → empty state]
*"Keine Daten verfügbar. Passen Sie die Filter an und klicken Sie auf **Generieren**."* — there is **no
Generieren control anywhere on the page** (all 45 visible buttons/links enumerated). Instruction cannot be
followed.

### ES#3 — Column-selector shows English while the table header beside it is translated · P3 · [FE] wire-up
[UI — Employee / Participant / Redemption Report → `.select-placeholder` 150px]
Table header `Fecha de incorporación` ✅ vs selector chip `Date of Joining (+5 others)` ❌ — **same concept,
same screen, same moment**. Redemption: `Fecha de transacción` vs `Transaction Date (+10 others)`.
**Why this is conclusive:** the correct string is proven present, loaded and rendering on the same page, so
"translation missing" is not an available explanation. Unambiguous wire-up defect.
Also **clips +31 / +48px in all six languages** — because untranslated English does not shrink.

## Configuration → Settings
### SET#3 — Settings cards overflow at 1024; **French is worst** · P3 · [FE]
`.banner-actions` (136px): **fr +87** > de +73 > pt +68 > en +67 > es +60 > pl +42 > zh 0.
Overturns the earlier CLEAN rating for this module (that rating concerned translation quality, which is
genuinely correct; the module was simply never measured for layout).

## Configuration → Add Employees
### AE#3 — Page header/dropzone overflows at 1024; Polish worst · P3 · [FE]
pl +356 > fr +347 > pt +306 > de +299 ≈ es +299 > zh +184.

## Community → Events
### EV#3 — 12 broken images · P3 · [FE-BE TBD] — identical in all languages/resolutions.

### EV#4 — Event tabs clipped; **NOT German-specific** · P3 · [FE]
[UI — Events → `mat-mdc-tab-label` container 470px]
**pl +177 (worst) · fr +136 · pt +127 · de clips · es +2 (fits) · zh 0.** No tab pagination in any language.
**Correction:** Run 7 concluded from Spanish that this was "strongly German-specific." It is not — it clips
in four languages. Priority should rise accordingly.
**Note:** does NOT reproduce at 1920, so it affects ≤1440 only — it is not the functional blocker I once
suspected (tabs are reachable on a normal desktop).

### EV#5 — Invite-count label spills · P3 · [FE] — fr +42px, pt +27px in a ~125px box.

## Communications → Publish Notifications
### PN#1 — Zero-headroom title box + two-column overflow; **all non-English, Spanish worst** · P3 · [FE]
[UI — Publish Notifications → `.notif-title` 150px]
Fits "Notification Title" at **exactly** 150px. **es +8 > de +3**; `.two-column-layout` es +516 > fr/pl/zh
+512 > de +415 > pt +395 > en +381.
**The title box clips at 1920 too** — a fixed 150px box does not grow with the viewport, so this is one of
only three width-independent layout defects in the engagement.
Overturns this module's earlier CLEAN rating (translation quality was correct; layout was never measured).

### PN#2 — French "est dans" clips where English "is in" fits · P3 · [FE]
[UI — Publish Notifications → audience operator, 50px box]
French localizes the operator to *est dans* and it **CLIPS +6px**; de/es leave it as English `is in`, which
fits.
**Triage dependency:** the box was sized for the untranslated string, so **fixing the CC#3/EV#1 audience
wire-up will introduce this clipping** wherever it is applied. Widen the box first.

## Communications → Send Custom Email
### SCE#2 — Two-column layout overflows at 1024 · P3 · [FE — responsive] — es +387, pt +395, pl +401, fr +364.

## Workforce Health
### WS#2 / WL#2 — filter chips clip in every language · P3 · [FE]
Wellness Leagues chips (110/100px): pl +65 > de +62 > es +58 > pt +55 > fr +53 > zh +28.
**ES#4 — the chips clip ONLY where the wire-up works.** On the other report surfaces the same filters stay
English (RPT#1) and therefore fit. **So fixing RPT#1 will introduce this overflow across all six report
surfaces — widen the chips before shipping the translation fix.** Wellness Leagues is a live preview of what
the others will look like.

### ES#2 — Mixed-language filter row on cold load · P3 · [FE]
Row reads *"País: All Countries"*, *"Departamento: All Departments"* — Spanish label, English value, adjacent
to a correctly-Spanish *"Todos los grupos de edad"*.

## Rewards → Upload Points
### UP#3 — Page container overflows +474px at 1024 · P3 · [FE — responsive] — same in all languages.

### UP#4 — A failed upload gives the admin NO feedback whatsoever · **P2** · [FE]
[Functional — Upload Points → submit]
`POST /api/v1/employee/reward/upload` returned **400** with a detailed, actionable body:
`Error in row 1: Domain validation failed: … , Award Amount not found or not an integer` — and **the UI
displayed nothing**: no toast, no inline error, no row highlighting (MutationObserver captured zero toasts).
**Expected:** surface the per-row errors the server already returned.
**Actual:** silence.
**Why it matters:** silent failure on a **data-writing** operation — an admin would conclude it worked. The
server is doing its job; the frontend discards the payload, so the fix is cheap.
**Inconsistency:** the same submit with a *client-side* error (missing country) DOES toast. Client-side
validation surfaces; server-side 4xx does not.
**Highest-value follow-up:** check whether other write flows (Add Employees, Create Content/Event/
Announcement, Send Custom Email) discard 4xx the same way — that would multiply one P2 across modules.

### UP#5 — Upload preview accumulates instead of replacing · **P2** · [FE]
[Functional/UX — Upload Points → CSV preview]
Selecting a second CSV renders a **second preview table below the first**; the previous file's rows remain.
Confirmed **2 visible `<table>` elements** (3 rows + 2 rows) after two selections.
**Expected:** preview reflects only the current file.
**Actual:** old and new data shown together, each with its own header row.
**Why it matters:** the real flow is upload → see errors → fix → re-upload. Exactly then the admin sees
stale rows mixed with new and **cannot tell which data will be submitted** — on a screen that grants points.
Combined with UP#4 the failure path is genuinely confusing.
**Evidence:** `evidence/de_uploadpoints_preview_accumulates.png`

### UP#6 — Validation toast hardcoded English · P3 · [FE] not-externalised
[Localization — Upload Points → submit without country]
Toast: **"Error / Please select a country"** in a German session.
`"Please select a country"` → **no key in any of the 4 dictionaries**. `"Error"` → German *"Fehler"* exists.
The module's own namespace **`pointsUpload.*` IS translated** (`pointsUpload.selectCountry` = *Land
auswählen*) — so the module is wired for i18n and only its validation message was missed.
**First error-state string ever captured in this engagement (dimension G8 was at zero) and it failed** —
suggests other validation messages are likely hardcoded too.

### UP#7 — Required field does not gate submit · P3 · [FE]
`Absenden` was `aria-disabled="false"` while required **"Land auswählen\*"** was empty. Deviates from the
documented app-wide preventive-validation pattern; validation is reactive on click.

### UP#8 — Sample CSV template has English headers and no UTF-8 BOM · P4 · [FE-BE TBD]
Downloaded template headers are English in a German session (`Receiver Employee Name`, `Point`, `Award
Name`), file is plain ASCII with **no BOM**.
**Note/Doubt:** the parser matches on those English headers, so localizing them would break upload unless
both sides change together. **Needs product decision**, not a quick fix.

## Cross-module — language/session behaviour
### ES#1 — Cold page load renders shared filter components in English · **P2** · [FE] init-order
[Functional/Localization — cross-module; confirmed on Content Library + Wellness Leagues]
On a **cold load** the filters render English; navigating away and back **to the same route** re-renders them
in the selected language. Same URL, same session, `fit_lang` unchanged.
| Surface | Cold load | After in-app nav |
|---|---|---|
| Content Library | `All` / `All` | `Todos` / `Todos` |
| Wellness Leagues | `All Countries` / `All Departments` | `Todos los países` / `Todos los departamentos` |
**Why the direction matters:** cold load is what a user gets from a bookmark, refresh or shared link — so the
**broken state is the default state**.
**Scope (measured, not assumed):** all 11 in-app-measured modules were re-checked cold and **only 1 of 11
differed** → component-specific, **not** systemic. Earlier sign-offs are NOT broadly invalidated.
**Distinct from RPT#1:** RPT#1 stays English cold *and* warm (hardcoded default); ES#1 goes correct once the
dictionary is warm (init-order race). **Two fixes, not one** — a dev treating them as one bug closes half.
**Evidence:** `evidence/contentlibrary_es_coldload_filters_english.png`
**Method rule adopted:** verify localization by **direct URL**, never by clicking the sidebar.

### CC#2 (extended) — date picker fully English in German at 1920 · P3 · [FE]
Calendar header `JUL 2026`; weekday headers `Monday…Sunday`; initials `M T W T F S S`. German needs
`Juli 2026` and `Mo Di Mi Do Fr Sa So` — **even the initials are wrong** (German = M/D/M/D/F/S/S).
Also the date **input format** is `30/07/2026` (placeholder `DD/MM/YYYY`) where German uses `30.07.2026` —
day-first, so not US format either.
**Evidence:** `evidence/de_1920_datepicker_english_calendar.png`

### CC#3 (extended) — audience widget: fuller English inventory · P3 · [FE]
Labels German (*Abteilung, Land/Region, Geschlecht, Altersgruppe*) but: operator **`is in` ×4**,
**`(+124 others)` / `(+14)` / `(+3)` / `(+5)` ×4**, and inside the dropdown **`All`** and **`Undisclosed`**
(should be *Alle* / *Nicht angegeben*). Country list all English (*Austria* not *Österreich*) — [BE] master
data. Gender/age values English — [BE].

### TERM#1 — Terminology split: *Herausforderung* vs *Challenge* · P3 · [Copy/Consistency]
[Consistency — cross-module, German]
| Surface | Term |
|---|---|
| Preview Emails | **Herausforderung**serinnerung, **Herausforderung**sstart, Abschluss der **Herausforderung** |
| Sidebar + wizard | Aktive **Challenges**, **Challenge** erstellen, **Challenge**-Name, **Challenge**-Slogan |
Same concept, two different words, both visible in one session. Needs a glossary decision applied
product-wide.

### REG#1 — Formal *Sie* against the product's informal *du* voice · P3 · [Copy/Register]
[Consistency — Create Challenge landing, German]
Heading: *"Erstellen **Sie Ihre** eigenen Neuen Challenges"* — formal register. Vantage Fit's voice is
informal *du* (same defect class as B12 on the employee web). *"Neuen Challenges"* is also oddly inflected
for a heading. **First concrete dashboard instance of the register split.**

### DEL#1 — No delete control exists for challenges · P4 / Enhancement · [FE]
Challenge cards expose only *Ansehen* and *Verwalten*. No delete anywhere → QA/test challenges (e.g. 25441)
are **permanently unremovable** and test data accumulates in the tenant forever. Raise with product.

## Identified and explicitly NOT bugs (recorded so they are not re-flagged)
- **Activity master list** (21 items: *Steps, Walking/Running, Water Intake, Mood-O-Meter, Active Minutes*…)
  renders English — **20 of 21 have no key in any dictionary** and it is served from
  `/vantagefit/api/v1/challenge/multiweek/config` → **[BE], expected English**. Nuance: `"Steps"` *does* have
  German *"Schritte"*, but under `reportCols.steps` / `contest.steps` (report + contest contexts), which does
  not prove this list should localize. A bare re-fetch returned 401, so the source is inferred from the
  endpoint + dictionary absence rather than read from the body. **Needs Product Confirmation** whether
  activity names are in localization scope.
- **`.fit-table-scroll` overflows** — `overflow:auto`, scrollable by design (see RPT#6).
- **Data-table / content-title overflows** on cards — authored content, not UI strings.
- **G5 comma-decimal in CSV** — server correctly rejects `12,5` as "not an integer". No silent corruption.
- **G6 non-ASCII + semicolon CSV** — umlauts/accents/carons render clean; the semicolon delimiter German
  Excel emits is auto-detected and parsed correctly. **Not a defect.**
- **German empty states** — correctly localized and natural.
## Accessibility (dimension G19 — first data ever collected, Run 13)

### A11Y#1 — Images have no `alt` text, at scale · P3 · [FE]  (CROSS-MODULE)
[Accessibility — Manage Challenges 103 · Past Challenges 24 · Create Challenge 9 · Events 1]
`alt` attribute absent entirely, so screen readers announce filenames or nothing.
**Expected:** meaningful `alt` on content images, `alt=""` on decorative ones.
**Actual:** attribute missing.
**Compounds MGC#4 / EV#3:** the broken images (5 and 12) also have no `alt`, so the user gets **neither the
image nor a text fallback**. Language-independent (same DOM in all six languages).

### A11Y#2 — Icon-only buttons with no accessible label · P3 · [FE]  (CROSS-MODULE)
[Accessibility — Create Event 4 · Create Announcement 2 · Publish Notifications 2]
No text content, no `aria-label`, no `title`. Extends SET#2 and CL#5 from single instances into a pattern.

### U7#1 — Date values English on EVERY date-bearing surface, incl. 2 mixed-language fragments · P3 · [FE]
[Localization — cross-module date formatter]
All 6 report date pickers `Jun 28, 2026 - Jul 27, 2026`; Manage Challenges `19 May 2025 - 17 May 2026`;
Past Challenges `13 Mar 2026`; Events `23 Oct 2024`; Content Library **`Friday 26 Jun`** (English weekday
AND month in a German page); Wellness Leagues **`Am 27 Jul 2026`** (German preposition + English month).
Extends RPT#4 / MGC#1 from "some modules" to **every** date-bearing surface. German needs `Juli` / `Freitag`.

### U7#2 — Currency renders `$` in a German session on an India tenant · P3 · [FE-BE TBD]
Overview shows **`$0`**. Neither the tenant (India, company 355) nor the session language implies USD.
Confirms OV#6 with a concrete symbol. **Needs Product Confirmation** which currency is intended.

### OV#4 — confirmed engagement-wide (was single-screen) · P3 · [FE]
`document.documentElement.lang` is **`"en"` on every module in all six languages**, regardless of
`fit_lang`. Screen readers apply English pronunciation to de/fr/pt/pl/zh content.

### PN#2 — extended: audience-operator box clips at 1920 too; Polish worst · P3 · [FE]
50px fixed box: **pl `należy do` +14px** · **fr `est dans` +6px** · de/es `is in` (untranslated) fits.
**The width-independent group is therefore FOUR components, not three:** `.notif-title` 150px, report
column-selector 150px, Wellness Leagues chips 110/100px, **audience operator 50px**. These are the only
layout defects affecting desktop users — fix these first.

## Dimensions tested and CLEAN (never tested before Run 13; all pass across 22 German modules)
- **U2 raw i18n keys** — none leaked anywhere.
- **U2 unresolved placeholders** — no `{0}`, `{{name}}`, `%s`.
- **U3 other-language bleed** — no cross-language string contamination.
- **U6 mojibake / tofu** — umlauts, accents, Polish diacritics and CJK all render correctly.

### U7#3 — "As-of date" mixed-language fragment in ALL SIX languages · P3 · [FE]
[Localization — Wellness Leagues → as-of date]
The prefix localizes correctly; the month never does:
`Am 27 Jul 2026` (de) · `El 27 Jul 2026` (es) · `Au 27 Jul 2026` (fr) · `Em 27 Jul 2026` (pt) ·
`Na dzień 27 Jul 2026` (pl) · `截至 27 Jul 2026` (zh-CN)
**Expected:** `Am 27. Juli 2026`, `El 27 de julio de 2026`, `截至 2026 年 7 月 27 日` etc.
**Why this is the cleanest evidence in the engagement:** the surrounding words prove the i18n wiring on this
very element is correct — so the defect is unambiguously the **date formatter**, not the translation layer.
Same root cause as U7#1 / RPT#4 / CC#2. **Fixing that one formatter resolves all of them in six languages
simultaneously.**

### RPT#7 — confirmed cross-language (was logged as German-only)
Chinese empty state: 「无可用数据。请调整筛选条件并点击"生成"。」 — instructs clicking "Generate" for a button
that does not exist, exactly as in German.

## Detector corrections (Run 14) — recorded so results can be trusted
- **Leaf-count load guard produced a FALSE INVALID.** Guarding on `leaves < 25` misfired on legitimately
  sparse report pages (empty states). Replaced with a **chrome-presence** check. The flaw withheld valid
  findings; it never invented clean ones.
- **U3 bleed detector produced FALSE POSITIVES.** It flagged `Próximo`/`Valor` as Portuguese bleed in a
  Spanish session, but both are the correct Spanish values (`manageChallenge.statusUpcoming`,
  `reportCols.value`) that merely coincide with Portuguese. Fixed by excluding values present in the active
  dictionary. **Neither was reported as a bug.** After the fix U3 is clean in all six languages.

---

# ARABIC (ar) — first ever test, 2026-07-28 (Run 15)

Arabic ships a **complete 991-key dictionary** and is **selectable in the production language list**, but had
never been opened in this engagement. It is the only RTL language among the 18 offered.

### AR#1 — Arabic is fully translated but rendered LEFT-TO-RIGHT; RTL is not implemented · **P2** · [FE]
[UI / Localization — global, all modules · check U5]

Arabic strings render correctly (72 Arabic strings on Overview, glyphs and shaping fine —
`آخر 30 يومًا`, `المستخدمين المسجلين`, `عرض المزيد`). **But no RTL direction is applied anywhere:**

| Check | Expected for RTL | Actual |
|---|---|---|
| `<html dir>` | `rtl` | **(absent)** |
| `body` computed direction | `rtl` | **`ltr`** |
| `main` computed direction | `rtl` | **`ltr`** |
| Elements with `dir="rtl"` | many | **0** |
| Sidebar position | right edge | **left (x = 80)** |
| "View more" arrows | point **left** (←) | **point right (→)** |

**Expected:** `dir="rtl"` on the document, mirrored layout (nav on the right), mirrored directional icons and
chevrons, right-anchored controls.
**Actual:** the page is a left-to-right layout containing Arabic text. Arabic *text runs* appear
right-aligned only because the browser applies bidi within a run — that is the browser doing it, not the app.
**Impact:** for an Arabic reader the entire information architecture reads backwards — navigation, card
order, progression arrows and control alignment all run the wrong way.
**Severity judgment:** logged **P2** on the CLAUDE.md scale (high-impact, broken user flow — no crash and no
data loss, so not P1 by the letter of the scale). **But it is effectively a market-readiness blocker:** an
entire locale is live, fully paid-for in translation, and structurally unusable. Recommend a product decision
on whether Arabic should remain user-selectable until RTL ships — that is a bigger question than the bug.
**Evidence:** `evidence/ar_rtl_not_implemented_overview.png`

### AR#2 — Mixed-language fragment inside one control · P3 · [FE]
The date control reads **`آخر 30 يومًا` | `Jun 28, 2026 - Jul 27, 2026`** — Arabic label beside an English
date range in the same widget. Same root cause as U7#1 / U7#3 (locale-unaware date formatter), now confirmed
in a 7th language.

### AR#3 — Numeral systems inconsistent within a single row · P3 · [FE-BE TBD]
The Wellness-score breakdown shows **Arabic-Indic numerals inside translated strings**
(`خطوط الأساس الصحية (٢٠٪)`, `المشاركة (٣٠٪)`) while the **data values on the same rows use Western digits**
(`89`, `44`, `33`). So one row mixes `٢٠٪` and `89`.
**Note/Doubt:** Western digits are common and often preferred in Arabic business UIs, so the *choice* is a
judgment call — but **mixing both systems in one row is not**. Needs a product decision on which numeral
system Arabic should use, then applied consistently. The Arabic-Indic digits are baked into the translation
strings, so this cannot be fixed in the formatter alone.

### AR#4 — `<html lang>` is `"en"` in Arabic too · P3 · [FE]
Same as OV#4 — now confirmed in a 7th language. Especially damaging for Arabic, where a screen reader needs
both the language **and** the direction to read correctly.

## A1 locale propagation — VERIFIED CLEAN (dimension never tested)
`POST /vantagefit/api/dashboard/v1/reports/employee-report/enrolled` carries **`accept-language: pl`** in a
Polish session — the FE correctly tells the API which language is selected.
**Why this matters for triage:** the backend *is* receiving the locale, so the untranslated backend strings
([BE] items throughout this log) are a **backend-scope decision, not a missing-header bug**. That removes an
entire hypothesis from the fix discussion.

---

# ALL 18 LANGUAGES COMPLETE — Run 16 (2026-07-29)
Detail: `bug-logs/all-18-languages.md`. Every language in the production selector has now been opened:
en · de · es · fr · fr-CA · pt · pl · zh-CN · ar · or · hi · ru · ko · vi · hu · nl · it · id.
All 18 dictionaries: **0 missing keys, 0 empty values.**

### FRCA#1 — fr-CA partially falls back to metropolitan French · P3 · [FE]
[Localization — cross-module; unique to fr-CA, the only regional-variant pair shipped]
An **fr-CA** session renders `RÉPARTITION DU SCORE`. `"Répartition du score"` exists **only in `fr.json`**
(`overview.scoreBreakdown`); fr-CA specifies *"Répartition du **pointage**"* and English is *"Score
Breakdown"* — so the UI resolved the **fr** value in an **fr-CA** session. Not explicable as an English leak.
**Partial, not total:** Quebec terms do render elsewhere in the same session (`main-d'œuvre`, `Balados`),
while the Content Library type filter still shows `Podcast` where fr-CA specifies `Balado`.
**Why it matters:** fr-CA is genuinely translated — 42 keys differ from fr with correct Québec terminology
(*Balado, pointage, mieux-être, main-d'œuvre*). That work was paid for and roughly half of the visible
differences never reach users. Cheap, high-satisfaction fix.

### HU#1 — Hungarian: worst overflow in the engagement · P3 · [FE]
`Alkalmazotti azonosító` overflows the 110px Wellness Leagues chip by **+119px** — **more than double** the
container. Beats ru +68, pl +65, de +62.

### RU#1 — Russian: worst break count at 1440 · P3 · [FE]
**8 breaks on Overview** (others 0–4). `Зарегистрированные` spills +32px in a 214px box.

### OV#7 — reproduced with cross-language evidence (upgraded from single-screen observation)
An in-place switch Italian → Indonesian left the Wellness Leagues chip showing Italian
`Tutte le fasce d'età` while the rest of the page was correctly Indonesian and `fit_lang = id`. A **cold
load** then showed the correct `Semua Kelompok Usia`. **Two languages visible on one screen simultaneously** —
the clearest reproduction in the engagement, and it re-confirms the rule: verify on a fresh load, never after
an in-place switch.

### U7#3 — now confirmed in FOURTEEN languages
The date affix localizes correctly every time; the month never does. Includes postpositional languages where
the affix correctly follows the date:
de `Am` · es `El` · fr/fr-CA `Au` · pt `Em` · pl `Na dzień` · zh `截至` · ru `На` · vi `Vào` · nl `Op` ·
it `Al` · id `Per` · **or `…ରେ`** · **hi `…को`** · **hu `…napon`** — all followed/preceded by `27 Jul 2026`.
**Strongest evidence in the engagement that the translation layer works and the date layer does not.** One
formatter fix resolves U7#1, U7#3, RPT#4, CC#2 and AR#2 in every language at once.

### Script rendering — ALL 18 PASS (no tofu, no mojibake, no missing glyphs)
Odia (`ସକ୍ରିୟ ଚ୍ୟାଲେଞ୍ଜ`, 526 strings) · Devanagari (`सक्रिय चुनौतियाँ`, 526) · Cyrillic
(`Активные челленджи`) · Hangul (`진행 중인 챌린지`) · Han (`进行中的挑战`) · Arabic shaping
(`المستخدمين المسجلين`) · heavy-diacritic Latin (vi/pl/hu/pt). Odia and Devanagari were the highest font-risk
candidates and both render correctly including conjuncts.

### Text-expansion ranking — use Hungarian and Russian as stress references, NOT German
`hu +119` › `ru +68` › `pl +65` › `de +62` › `es +58` › `pt +55` › `fr/nl/it/or +53` › `hi +49` ›
`id +48` › `zh +28` › `ko +13`. **German ranks 4th** — the third independent demonstration that
"test German because it's longest" is unsafe for this product.

---

# DEEP-TIER UPGRADE es/fr/pt/pl/zh — Run 17 (2026-07-29)
Detail: `bug-logs/deep-tier-es-fr-pt-pl-zh.md`

### F6#1 — Search folds case but NOT diacritics · P3 · [FE]  (CROSS-MODULE, ALL 18 LANGUAGES)
[Functional — Content Library search / shared search component] · **dimension F6 never tested before**
`Youtube` → 2 rows · **`Youtubé` → 0** · `Video` → 1 · **`Vídeo` → 0** · `VIDEO` → 1 (case-folding works).
**Expected:** accent-insensitive matching, consistent with the case-insensitivity already implemented.
**Actual:** the same normalisation lowercases but ignores diacritics.
**Impact:** in every accented locale users type both with and without accents; *Nutrición* will not be found
by typing *Nutricion*. Likely a one-line fix (NFD normalise + strip combining marks).
**Evidence note:** the tenant has only ASCII titles, so the **mechanism** was proven (no folding either
direction) rather than a real-world miss observed. States the limitation honestly.

### SET#4 — Out-of-range value blocks save silently · P3 · [FE]
[Functional/UX — Settings → max team size]
`9999` (max 500) leaves the field invalid; clicking Save does **nothing** — no success toast, no error, and
Save is **not** `aria-disabled`. **Data integrity is safe** (reload confirmed 500 persisted). The defect is
feedback-only, and it is the **same silent-failed-write pattern as UP#4** — evidence of a systemic gap in how
this app surfaces write failures.
Also: the max clamp is **inconsistent** (`9999`→`500` once, stayed `9999` twice); not logged separately since
the value never persists, but validation feedback is unpredictable.

### F4 — CRUD + toast localization: PASSES in all five languages (positive result)
`Configuración guardada correctamente.` (es) · `Paramètres enregistrés avec succès.` (fr) ·
`Configurações salvas com sucesso.` (pt) · `Ustawienia zapisane pomyślnie.` (pl) · `设置保存成功。` (zh).
All reverted to 500. Confirms save/create toasts localize while upload/announcement/loading toasts do not —
now across five languages, not German alone.

### F1/F2 — filter interaction PASSES (es representative); `Undisclosed` still English inside the dropdown (CC#3).

### Layout @1366 completes the 4-width matrix for all five
PN `.two-column-layout` degrades progressively: **+512px @1024 → +170px @1366 → 0 @1920** — confirming it as
a pure responsive defect (OV#8b class), not a translation defect. Wellness Leagues chip:
pl +65 > es +58 > pt +55 > fr +53 > zh +28. **Settings and Events clean at 1366 in all five.**
`1 language` clips +7px in a 54px box in every language (untranslated English).

---

# DEEP-TIER ARABIC — Run 18 (2026-07-29)
Detail: `bug-logs/deep-tier-arabic.md`

### AR#1 — CONFIRMED GLOBAL (was a single-screen finding) · P2 · [FE]
RTL audit run on **all 9 modules** (Overview, Manage/Past Challenges, Events, Publish Notifications,
Settings, Content Library, Wellness Leagues, Employee Report). **Identical on every one:** `<html dir>`
absent, `body`/`main` computed `ltr`, **0** elements with `dir=rtl`, text-align resolving LTR.
**No partial RTL support and no module-level exception — RTL is not implemented anywhere in the product.**
Dropdown panes anchor left-of-trigger (correct LTR, wrong RTL) as a direct consequence.

### AR#3 — UPGRADED: both numeral systems inside the SAME string · P3 · [FE-BE TBD]
`٧٣ المستخدمون غير نشطين لمدة تزيد عن 30 يومًا` — **Arabic-Indic `٧٣` and Western `30` in one sentence.**
Also `ينتهي خلال ٤٣ أيام` (Arabic-Indic, from translation) vs `0 مشاركًا` (Western, from data), and
`خطوط الأساس الصحية (٢٠%)` (Arabic-Indic with Western `%`).
**Root cause identified:** translation strings were authored with Arabic-Indic numerals while runtime values
are injected as Western digits, so any string interpolating a number mixes both. **Not fixable in the
formatter** — the numerals are baked into `ar.json`. Needs a product decision then a dictionary re-author.

### Arabic PASSES — F4 CRUD + toast, F1/F2 filters, script rendering
Save `حفظ الإعدادات` · discard `تجاهل` · toast **`تم حفظ الإعدادات بنجاح.`** — all localized; 500→250→500
reverted. Filter dropdown opens/applies with Arabic options; `Undisclosed` still English (CC#3). Arabic
shaping and ligatures render correctly (526 strings on Manage Challenges), no tofu/mojibake — **the glyphs
are fine, only the direction is wrong.**

### Arabic layout across 4 widths — direction is the problem, not length
1024: Overview 7 breaks / CL 6 / PN 2 · 1366: Overview 3 / PN 2 · 1440: PN 2 · **1920: only the two
fixed-width components** (chip +35, column-selector +31). Arabic's chip overflow (+35) sits between Chinese
(+28) and Hindi (+49), far below Hungarian (+119).
**Blocked follow-up:** icon/chevron mirroring, logical padding, table column order and slider direction
cannot be meaningfully audited until `dir=rtl` exists. **Re-test Arabic layout after RTL ships** — expect a
fresh crop of bugs then.

### AR#3 — ROOT CAUSE CORRECTED (Run 18b) · P3 · [FE]
I first stated the Arabic-Indic numerals were "baked into `ar.json`" and "not fixable in the formatter".
**That was wrong.** `ar.json` contains **0 Arabic-Indic digits across all 991 keys** (verified twice) and 24
keys with Western digits. Meanwhile `(73).toLocaleString('ar')` → `73` but `(73).toLocaleString('ar-EG')` →
**`٧٣`**. So the on-screen Arabic-Indic digits are produced **at runtime** by a locale-aware formatting path
while other numbers render as raw Western digits.
**Corrected root cause: inconsistent runtime number formatting — fixable in the formatter, no dictionary
re-author needed.** This changes the fix owner and effort. A product decision on which numeral system Arabic
should use is still required, then applied to one code path.

### AR#5 — Arabic addresses every user as grammatically masculine · P3 · [FE content / U9]
Whole dictionary (991 keys). Masculine singular throughout with **zero** feminine forms:
`اختر` (choose, masc) ×25 · `حدد` ×16 · `أدخل`/`ادخل` ×11 · `انقر` ×6 · masculine possessive `ـك` ×41
(`مدير حسابك`) · `أنت` ×3 (`هل أنت متأكد؟`) · **feminine imperatives: 0**.
**Expected:** gender-neutral phrasing (verbal nouns, e.g. `الاختيار` not `اختر`) or gendered variants.
**Actual:** every female admin is addressed in the masculine.
**Assessment:** a content/translation-quality decision for the localization vendor, not a code defect — hence
P3. Consistent across all 991 keys, so deliberate or default rather than sporadic.

### Arabic politeness register — PASSES ✅
`يرجى`/`الرجاء` used consistently across 18 strings; **no formal/informal mixing**, unlike German (REG#1) and
the employee web (B12).

### ANN#1/#2 — confirmed in Arabic: Create Announcement renders ZERO Arabic strings
21 visible leaves, `arabicStrings: 0` — the whole module is untranslated in Arabic, consistent with the
"full dictionary, no wire-up" pattern logged for German.

### Method note (recorded so the numbers can be trusted)
The first Arabic register scan returned 0 for every marker — **a false negative**, not a clean result,
because JS `\b` word boundaries misfire on Arabic script. Re-run without `\b` gave the counts above. One
substring false positive was also excluded: `حددي` matched `المحددين` (masculine plural participle), so the
true feminine count is 0.

---

# DEEP-TIER: the 10 remaining languages — Run 19 (2026-07-29)
Detail: `bug-logs/deep-tier-remaining-10.md`. `ru hu ko vi nl it id or hi fr-CA` now have CRUD + expanded
modules + widths 1024/1440/1920.

### F4 CRUD + toast: PASSES in all 10 (positive result)
All ten produced a fully localized success toast and all ten reverted to 500 — e.g. Russian
`Настройки успешно сохранены.`, Korean `설정이 성공적으로 저장되었습니다.`, Odia
`ସେଟିଂସ୍ ସଫଳତାର ସହ ସେଭ୍ ହେଲା।`, Hindi `सेटिंग्स सफलतापूर्वक सहेजी गईं।`.
**fr-CA cleared of FRCA#1 here:** its toast matches fr, but `settings.saved`/`settings.save`/
`settings.discard`/`common.discard` are **identical in fr and fr-CA by design**, so this is not a fallback.
FRCA#1 still rests solely on `overview.scoreBreakdown`.

### PN#2 re-ranked — worst is INDONESIAN, not French · P3 · [FE]
The 50px audience-operator box: **id `termasuk dalam` +55px** › hu +29 › pl +14 = ko +14 › fr/fr-CA +6 ›
or +5. German and Spanish fit only because they leave the operator as untranslated English.
**PN#1 `.notif-title` (150px) worst is RUSSIAN +21px** › es +8 › de +3.
**Wellness Leagues chip worst is HUNGARIAN +119px** › nl 73 › ru 68 › pl 65 › it 63 › de 62 › … › ko 25.
**Triage consequence:** any fix sizing these fixed-width boxes must be validated against **Indonesian,
Hungarian and Russian** — not German or French, which are mid-pack or artificially "passing".

### Leak set proven language-independent at module granularity
Russian (19 modules) and Hungarian (17) produced **identical per-module leak counts**, and the other eight
matched on every module tested. So RPT#1 / CL#1 / CC#1 / ANN / `Reward` / `Employee ID` / OV#12 are a
**wire-up problem, not a translation problem — fixing each once fixes all 18 languages.**
Upload Points `58px SPILL @1086` is likewise identical in all 10.

### Overview confirms the OV#8a/OV#8b split for all 18
Overview is **clean at ≥1440 in every language** and breaks only at 1024 — exactly the responsive-vs-
localization split established earlier.
**⚠️ Vietnamese Overview measured 0 breaks at 1024** where all others showed 5–9 — flagged as an outlier
needing re-measure, **not** claimed as a pass.

### OV#7 reproduced a SECOND time (method note)
The Indonesian Wellness Leagues chip rendered **`Minden korcsopor` (Hungarian)** in an `id` session after an
in-place switch; that figure is excluded from the Indonesian data. Second independent reproduction of OV#7
(first was Italian-in-Indonesian). Re-confirms: **verify on a fresh load, never after an in-place switch.**
