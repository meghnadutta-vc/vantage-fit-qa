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
| **P1** | 0 | — |
| **P2** | 13 | OV#1, OV#2 · CC#1 · RPT#1, RPT#2 · CL#1 · EV#1 · CRC#1, CRC#2 · ANN#1, ANN#2 · ED#1 · WS#1 |
| **P3** | 19 | OV#3, OV#5, OV#6, OV#7 · CC#2, CC#3, CC#4, CC#5 · MGC#1, MGC#2 · RPT#3, RPT#4, RPT#5 · CL#2, CL#3 · EV#2 · ANN#3 · SCE#1 · WL#1 · AE#1, AE#2 · UP#1, UP#2 · PE#1 · DF#1 · FR#1 · OV#4(a11y, cross-module) |
| **P4** | 4 | SET#1, SET#2 · CL#4, CL#5 |
| Blocked | 1 | Health Insights (external iframe) |

Clean modules (0 bugs): **Past Challenges · Publish Notifications** (Settings & Upload Points static-clean; dynamic bugs added).

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
