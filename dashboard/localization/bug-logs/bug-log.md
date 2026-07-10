# Bug Log — Web Dashboard / Localization

Running bug log for Vantage Fit **web dashboard localization** testing.
Bug format & severity scale per [`../../../CLAUDE.md`](../../../CLAUDE.md).
Bugs numbered sequentially (`Bug #1, Bug #2 …`); **crashes/P1 listed first**. Append per run — never overwrite.

---

## Run 1 — 2026-07-10 — German (de), Overview + global chrome

**URL:** `https://dashboard-v2.vantagecircle.co.in/fit/overview` · **Baseline:** English (en)
**Access:** employee app → profile → HR Admin Dashboard (token handshake) → `/fit/*`.

### Bug #1
[Functional/Copy - P3]
[Vantage Fit Admin — Overview → top filter bar]
The country filter control renders in English while the UI language is German. Every other
nav/chrome string on the page is translated, so this is a missing-translation string, not a
design choice.

Expected: German label, e.g. "Alle Länder"
Actual: "All Countries" (English) — switcher set to German ("Inhaltssprache: German")
Note/Doubt: Confirm the country filter is FE-owned (externalised in VF-2206); it is UI chrome so should be in the FE bundle.
Evidence: evidence/de/overview_landing.png · evidence/en/overview_landing.png

### Bug #2
[Functional/Copy - P3]
[Vantage Fit Admin — Overview → date-range control]
The date-range preset label renders in English in German mode.

Expected: German label, e.g. "Diesen Monat"
Actual: "This Month" (English)
Evidence: evidence/de/overview_landing.png

### Bug #3
[Functional - P3]
[Vantage Fit Admin — Overview → date-range value]
The date-range value is not locale-formatted for German (English month abbreviation and
comma/order instead of German dotted, day-first convention).

Expected: e.g. "01.07.2026 – 09.07.2026"
Actual: "Jul 01, 2026 - Jul 09, 2026"
Note/Doubt: Verify date formatting is client-side (`Intl` + locale). If server-supplied, moves to backend phase; if client-side, in-scope FE formatting bug.
Evidence: evidence/de/overview_landing.png

### Bug #4
[Copy - P3]
[Vantage Fit Admin — left rail → Language selector ("Sprache" / "Inhaltssprache")]
The language dropdown's own option labels stay in English in German mode: selected value shows
"German" (not "Deutsch") and the full option list ("English", "Arabic", "German", …) is
untranslated.

Expected: Localized language names (e.g. selected = "Deutsch")
Actual: All options English; selected value "German"
Note/Doubt: Showing each language in its own endonym (Deutsch, Français, 日本語) is a common alternative convention — confirm intended behavior. Either way, all-English in German mode is inconsistent.
Evidence: evidence/de/overview_landing.png

### Bug #5
[Copy - P4]
[Vantage Fit Admin — left rail → plan badge]
The plan badge label is not translated in German mode.

Expected: German for the "Active Plan" portion, e.g. "Aktiver Plan – Grow" ("Grow" is a plan-tier brand name, may stay)
Actual: "Active Plan - Grow" (English)
Evidence: evidence/de/overview_landing.png

---

### Notes / Doubts — Run 1 (not confirmed defects; need dev/design confirmation)

- **N1 — Global product rail untranslated.** Left-most product switcher (Recognition, Wellness,
  Pulse, Redemption, Milestone, Perks, Admin Hub) stays English in German mode. This is the
  shared dashboard-v2 shell, likely NOT part of the admin-Fit i18n bundle. Ownership question:
  is the global shell in scope for Fit localization? If yes → bug; if no → out of scope.
- **N2 — "Ask Vantage Fit" chat widget untranslated.** "Ask Vantage Fit anything" + suggested
  prompt render in English. Console shows a separately-loaded embedded widget
  (`dl.vantagecircle.com/vfit-chat/…iife.js`) — likely its own i18n lifecycle, possibly out of
  VF-2206 scope. Confirm ownership.
- **N3 — Switcher labeled "Content language"/"Inhaltssprache" but controls the whole UI.** It
  re-renders all admin chrome (AC1 passes), so the "Content" qualifier may mislead admins. Minor
  copy/UX call.

### Non-localization observation (awareness only, not a loc bug)
- **O1 — Overview dashboard body does not render** (perpetual skeleton loaders) in BOTH English
  and German. Console: repeated `TypeError: Cannot read properties of undefined (reading 'every')`
  in `disableRange` (date-picker, chunk-5U75MZAC.js). Pre-existing/environment, identical across
  languages → not a localization defect. Consequence: chart/stat card content & labels on
  Overview could not be tested for localization. Evidence: evidence/{en,de}/overview_landing.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), Create Challenge

### Bug #6
[Copy/Functional - P3]
[Vantage Fit Admin — Create Challenge → "Create your own" challenge-type cards]
The five challenge-type cards render their titles **and** descriptions in English in German mode,
while the surrounding instructional text on the same screen is German. These are static product
UI strings (not user data), so they should be in the FE bundle.

Expected: German titles + descriptions (e.g. "Benutzerdefinierte Challenge", "Wettlauf-Challenge" …)
Actual (all English): "Custom Challenge" / "Do it yourself: configure every task and target individually." · "Race Challenge" / "A wellness competition driven by a realtime leaderboard…" · "Journey Challenge" / "A wellness competition driven by a real-time leaderboard…" · "E-Marathon" / "A unique challenge format… 1000 steps as 1 km…" · "Streak Challenge" / "A simple competition may end up being a boring race…"
Note/Doubt: Confirm the type names ("E-Marathon" etc.) are meant to be translated vs. kept as feature brand names; the **descriptions** are clearly descriptive copy and should be translated regardless.
Evidence: evidence/de/create-challenge_landing.png

### Bug #7
[Copy - P4]
[Vantage Fit Admin — Create Challenge → section heading]
German capitalization error in the section heading: an adjective is incorrectly capitalized.

Expected: "Erstellen Sie Ihre eigenen neuen Challenges" (adjective "neuen" lowercase)
Actual: "Erstellen Sie Ihre eigenen **Neuen** Challenges"
Evidence: evidence/de/create-challenge_landing.png

### Bug #8
[Copy - P4]
[Vantage Fit Admin — badge inconsistency (nav vs Create Challenge)]
Badge localization is inconsistent: the "New" badge is translated to "NEU" on the Create
Challenge template cards, but the same-meaning badges in the left nav stay English ("NEW" on
Wellness Score, "FREE" on Content Library).

Expected: Consistent treatment — either all localized ("NEU", "KOSTENLOS") or all kept as brand tags
Actual: "NEU" (template cards) vs "NEW" / "FREE" (nav)
Evidence: evidence/de/create-challenge_landing.png · evidence/de/overview_landing.png

---

### Notes / Doubts — Create Challenge
- **N4 — Pre-built template names & descriptions are English (BE content, deferred).** Cards
  "Stress Free Month", "Elevate Endurance", "Mindful Moving", "Healthy Habits Hero" and their
  descriptions ("Join our HR team for a month-long campaign, 'Embrace Serenity.'" etc.) render
  in English. These are dynamic content/template data, not FE chrome → **⭕ BE-deferred**, not a
  Phase-1 bug. Evidence: evidence/de/create-challenge_landing.png

### Positive verification (Create Challenge)
- Custom Challenge **builder (step 1) is fully localized** and grammatically correct in formal
  German: field labels (Challenge-Name, Challenge-Slogan, "Über die Challenge",
  Geschäftsbedingungen), placeholders ("Wird in der App angezeigt", "Maximal 2000 Zeichen
  erlaubt"), the auto-announce toggle + consent sentence, and Zurück/Weiter buttons. No layout
  breakage; formal "Sie" tone used consistently. Evidence: evidence/de/create-challenge_custom_builder_step1.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), Active Challenges (/fit/manage-challenge)

### Bug #9  ⭕ BACKEND-DEFERRED (verified — not a Phase-1 FE defect; kept for visibility)
[Copy/Functional - P3 · BACKEND]
> Verified backend-sourced: FE renders API field `statusString` verbatim (see "FE/BE SOURCE VERIFICATION"). Expected English until backend translation lands. Tracked here for visibility, owned by the backend localization ticket.
[Vantage Fit Admin — Active Challenges → challenge card status badges]
Status/time badges are only **partially** localized: on the same card, "Bevorstehend"
(upcoming) and "Privat" (private) are German, but the time/status badges stay English. Because
sibling badges are translated, this is an inconsistent missing-translation, not a design choice.
Counts on this page: "Ended" ×38, "Ends in 6/38/62/102/104 Days" ×many, "Active" ×3.

Expected: German, e.g. "Endet in 6 Tagen", "Beendet", "Aktiv"
Actual: "Ends In 6 Days", "Ends In 38 Days", "Ended", "Active" (English)
Evidence: evidence/de/manage-challenge_list.png

### Bug #10  ⭕ BACKEND-DEFERRED (verified — not a Phase-1 FE defect; kept for visibility)
[Copy/Functional - P3 · BACKEND]
> Verified backend-sourced: FE renders API field `challengeTypeName` verbatim (source `/vantagefit/api/v1/get/ongoingCampaigns`). Expected English until backend translation lands. Owned by the backend localization ticket.
[Vantage Fit Admin — Active Challenges → challenge type/format badges]
Challenge type/format badges render in English in German mode: "Multi Week Multi Activity"
(×46), "Race", "Journey". These are a finite set of product format labels (same family as the
Create-Challenge type cards, Bug #6).

Expected: German format labels
Actual: "Multi Week Multi Activity", "Race", "Journey" (English)
Note/Doubt: Confirm whether format names are intended as translatable labels or brand terms — treat consistently with Bug #6.
Evidence: evidence/de/manage-challenge_list.png

### Bug #11
[Functional - P3]
[Vantage Fit Admin — Active Challenges → challenge card date ranges]
Card date ranges use English month abbreviations and are not German-formatted (same root cause
as Bug #3, different location).

Expected: German format, e.g. "10.07.2026 – 16.07.2026" (or "10. Juli 2026")
Actual: "10 Jul 2026 - 16 Jul 2026", "07 Jul 2026 - 17 Aug 2026", "04 Jun 2026 - 10 Jun 2026"
Evidence: evidence/de/manage-challenge_list.png

---

### Notes / Doubts — Active Challenges
- **N5 — Singular pluralization of "Teilnehmende".** Count label shows "1 Teilnehmende" for a
  single participant; German grammar prefers a singular form ("1 Teilnehmende:r" / "1 Teilnehmer"
  / "1 teilnehmende Person"). Classic i18n plural-rule gap (0/1/many). Low priority; flag for
  native-speaker + ICU plural review. Evidence: evidence/de/manage-challenge_list.png
- Challenge **names** (Badge Award, Custom Challenge - I, testst, Test May, fccutcuy, gfhf,
  "Journey to the top of the world") are user/content data → ⭕ BE-deferred, not bugs.

### Positive verification (Active Challenges)
- Page chrome fully German: heading "Aktive Challenges", subtitle "Verwalten Sie Ihre laufenden
  und bevorstehenden Wellness-Challenges" (formal Sie), section headers "Laufende Challenges" /
  "Bevorstehende Challenges", "Teilnahme", "N Teilnehmende", "Privat", row actions "Ansehen" /
  "Verwalten". No layout breakage. Evidence: evidence/de/manage-challenge_list.png

### Cross-check (Past Challenges — /fit/past-challenges)
- Chrome German: "Vergangene Challenges", subtitle "Überprüfen Sie abgeschlossene Challenges und
  deren Leistungskennzahlen", status "Abgeschlossen" (Completed) ✅. **Reinforces Bug #9's
  inconsistency:** an ended challenge shows translated "Abgeschlossen" here but English "Ended"
  on the Active page. Same #10 ("Multi Week Multi Activity") + #11 (English dates: "13 Mar 2026")
  apply. No new bug classes. Evidence: evidence/de/past-challenges_list.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), Engage → Programs (Content Library + Create Content)

### Bug #12
[Copy/Functional - P2]
[Vantage Fit Admin — Content Library → "Create content" modal (content-type chooser)]
The entire Create-content chooser dialog renders in English while the page behind it is German.
This is a whole untranslated dialog on a primary content-authoring entry point.

Expected: German, e.g. heading "Inhalt erstellen", prompt "Was möchten Sie erstellen?", options "Verknüpfter Inhalt" / "Health Bite" with German descriptions
Actual: heading "Create content"; prompt "What would you like to create?"; option 1 "Linked Content" — "Add an article, video or podcast link."; option 2 "Health Bite" — "Author a bite-size content experience."
Evidence: evidence/de/create-content_modal.png

### Bug #13
[Copy/Functional - P3]
[Vantage Fit Admin — Content Library → list filter dropdowns]
Both list filter dropdowns default to the English label "All" in German mode (Type filter and
Category filter).

Expected: "Alle"
Actual: "All" (×2)
Evidence: evidence/de/content-library_list.png

### Bug #14
[Copy - P3]
[Vantage Fit Admin — Content Library → table "Typ" column vs content-summary panel]
The content-type value is inconsistent within the same screen: the table "Typ" column shows
"Article" (English) while the right-hand content-summary panel and the create modal's filter
show "Artikel" (German). "Bite Size" also stays English in the summary panel.

Expected: Consistent German type labels ("Artikel", "Video", "Podcast", "Bite Size" → confirm)
Actual: "Article" (table) vs "Artikel" (summary/modal); "Bite Size" (summary) English
Note/Doubt: Confirm whether content "Typ" values are FE enum labels (translatable) or backend data. The "Artikel" rendering elsewhere suggests they are translatable and the table cell is the outlier.
Evidence: evidence/de/content-library_list.png · evidence/de/create-content_modal.png

---

### Notes / Doubts — Programs
- **N6 — Accessibility (all languages).** Content Library table row-action icon buttons have no
  accessible name (empty label in the a11y tree). Not localization-specific (affects English
  too), but flagged per the accessibility checklist. Evidence: evidence/de/content-library_list.png
- Content titles ("Test Conetnt Title…", "Youtube link…") and category values ("Mindfuless"
  [sic], "Excercise" [sic], "Healthy Eating") are content data → ⭕ BE-deferred (the [sic]
  misspellings are content-data issues, out of localization scope).

---

## Run 1 (cont.) — 2026-07-10 — German (de), Engage → Community

### Bug #15
[Copy/Functional - P2]
[Vantage Fit Admin — Community → Create Announcement (/fit/community/announcement)]
The entire Announcements page body renders in English in German mode — heading, description,
info banner, list section, search, column header, row actions, and the primary CTA. Only the
left nav (which links here as "Ankündigung erstellen") is German. A whole admin screen is
unlocalized.

Expected: German throughout (e.g. "Ankündigungen", "Verfassen und veröffentlichen Sie Ankündigungen für Ihre Organisation.", "Ankündigung erstellen", "Was ist eine Ankündigung?", "Vorhandene Ankündigungen", "Nach Titel suchen…", "Titel", "Ankündigung löschen", "Mehr anzeigen")
Actual (all English): "Announcements" · "Write and publish announcements to your organisation." · "Create Announcement" · "What is an Announcement?" · "Send messages about new initiatives, events, and more to your organisation" · "Existing Announcements" · "Search by title..." · column "Title" · "Delete announcement" · "Show more"
Note: No layout breakage — purely a missing-translation gap. Row values "Test Announcement" are content data (BE).
Evidence: evidence/de/announcement_page.png

---

### Positive verification — Community
- **Create Event (/fit/events/create-event): fully localized.** All section/field labels German
  and correct: "Grundlegende Informationen", "Veranstaltungstitel", "Start-/Enddatum/-zeit der
  Veranstaltung", "Zielgruppe", "Land", "Stadt", "Altersgruppe", "Abteilung",
  "Veranstaltungsort", "Über diese Veranstaltung", "Vortei l dieser Veranstaltung", "Häufig
  gestellte Fragen", placeholders ("Schreiben Sie Ihre Frage hier"), toggle + Zurücksetzen /
  "Neue Veranstaltung erstellen". Date inputs use German day-first "DD/MM/YYYY". No layout
  breakage. Evidence: evidence/de/create-event_form.png
- **View Events (/fit/events): chrome fully German.** Heading "Veranstaltungen anzeigen",
  subtitle, tabs "Laufende / Kommende / Vergangene Veranstaltungen", card labels "Anzahl
  gesendeter Einladungen", "Nutzerengagement", "N von N Teilnehmern aktiv", "Mehr erfahren".
  Only recurring issue: English date format "23 Oct 2024 - 29 Oct 2024" (Bug #11 class).
  Evidence: evidence/de/view-events_list.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), Engage → Communications

### Bug #16
[Copy/Functional - P2]
[Vantage Fit Admin — Communications → Email Designer ("Rich Email Composer" dialog)]
The Email Designer opens a full-screen "Rich Email Composer" dialog that is entirely in English
in German mode — title, value proposition, step names, action buttons, and the guidance cards.
Another whole feature surface unlocalized.

Expected: German throughout
Actual (all English): "Rich Email Composer" · "Send updates people actually open." · "People-first email" · "Build a polished, on-brand email in a few guided steps - then send it from your own mailbox…" · steps "Intro / Write / Design / Send" · "Continue last email" / "Keep the current draft." · "Start new" / "Begin from a fresh template." · cards "System mail gets skimmed", "Your mailbox lands harder", "Designed, not plain" · "Get started" · "Import template" · "Close"
Note/Doubt: The live-preview pane content ("A clear headline for your update", "Company update"…) is template placeholder copy; confirm whether that is FE chrome or template data. The dialog chrome itself is clearly FE and should be translated.
Evidence: evidence/de/email-designer_dialog.png

---

### Positive verification — Communications
- **Publish Notifications (/fit/community/publish-notifications): fully localized, high quality.**
  "Benachrichtigung veröffentlichen", "Senden Sie gezielte In-App-Benachrichtigungen an
  Mitarbeitende.", "Benachrichtigungsinhalt", "Titel"/"Untertitel" + placeholders + char counters,
  "Zielgruppe" (Benutzer auswählen / Attribute / CSV-Upload), attribute filters fully German
  incl. **"Alle Länder", "Alle Abteilungen", "Alle Geschlechter", "Alle Altersgruppen"** (this
  confirms Overview Bug #1 "All Countries" is a genuine gap), live preview ("Vorschau", "Gerade
  eben", "Desktop-Ansicht"). No layout breakage. Evidence: evidence/de/publish-notifications_form.png
- **Send Custom Email (/fit/community/send-custom-email): fully localized.** "Benutzerdefinierte
  E-Mail senden", "E-Mail-Inhalt" (Betreff / Überschrift / Text + counters), "Zielgruppe", "Aus
  Bericht erstellen" (Liga-Bericht / Mitarbeiterbericht / Teilnahmebericht / Wellness-Score),
  "Nach Name oder E-Mail suchen…", "E-Mail-Vorschau" ("Posteingang", "Gerade eben"). The
  **previewed email template body** ("Download the Vantage Fit app") is English → ⭕ email-template
  scope (separate/deferred), not the FE form. Evidence: evidence/de/send-custom-email_form.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), Analyze → Workforce Health

### Bug #17  ⭕ BACKEND-DEFERRED (verified — not a Phase-1 FE defect; kept for visibility)
[Copy/Functional - P2 · BACKEND]
> Verified backend-sourced: the stat cards, chart titles, and legends are served by `/vantagefit/api/dashboard/v1/workforce-health/wellness-score/insights/stream` as `header`/`label` fields and rendered verbatim. Only the page heading/subtitle are FE (correctly German). Expected English until backend translation lands. Owned by the backend localization ticket.
[Vantage Fit Admin — Workforce Health → Wellness Score (/fit/workforce-health/wellness-score)]
The Wellness Score screen is largely unlocalized: only the page heading + subtitle and a few
scattered words are German; the stat cards, chart titles/descriptions, legends, and filters are
English. One string is mixed mid-sentence.

Expected: German throughout
Actual — German: heading "Wellness-Score", subtitle "Misst Verbesserung und Konstanz Ihrer Belegschaft", "Einblicke", "KI-generiert", "Dieser Monat". English: stat cards "Current Score", "-31 vs Industry", "12-Month Average", "Based on monthly scores", "Industry Benchmark", "-31 below benchmark", "Based on anonymized industry data"; chart titles "Org Wellness Score Trend", "How the Wellness Score is Composed", "Component Trends Over Time", "Wellness Score by Department / Geography / Age Group / Gender" + their descriptions; legends "High (>=80)", "Moderate (70-80)", "Low (<70)"; filters "All Countries / All Departments / All Age Groups / All Genders"; "Correlation: Challenges & Programs Impact".
Mixed string (bug): "Current period breakdown • **Gesamtpunktzahl**: 43" (English + German in one line).
Evidence: evidence/de/wellness-score_page.png

### Bug #18
[Copy/Functional - P3]
[Vantage Fit Admin — Workforce Health → Wellness Leagues (/fit/workforce-health/wellness-leagues)]
Mostly German, but with English pockets. Filter **labels** are German ("Land", "Abteilung",
"Altersgruppe", "Geschlecht") while their **default values** are English.

Expected: German values + strings
Actual (English): filter values "All Countries", "All Departments", "All Age Groups", "All Genders"; helper "Based on avg daily steps over 21 days"; column name "Employee ID"; date "Am 09 Jul 2026" (English month).
German (correct): "Wellness-Ligen", subtitle, "Aktuelle Stufenverteilung", "Stufentrends im Zeitverlauf", "Wöchentlich"/"Monatlich", "Spalten", "Exportieren", empty states ("Keine Daten verfügbar", "Passen Sie Ihre Filter oder den Datumsbereich an").
Evidence: evidence/de/wellness-leagues_page.png

---

### BLOCKED — Workforce Health → Health Insights
- **Health Insights (/fit/workforce-health/health-insights): BLOCKED.** The page embeds an
  external dashboard (`dash-vfit.vantagecircle.org`) in an iframe that **"refused to connect"** in
  the MCP browser (frame blocked). Content not testable here. Note: this is a separate embedded
  app, so its localization is likely a different system than the VF-2206 admin bundle. Needs a
  browser/env where the iframe loads, or direct access to that app. Evidence: evidence/de/health-insights_blocked.png

### Cross-screen inconsistency (recurring — evidence for Bugs #1/#3)
- Filter "All Countries/Departments/Age Groups/Genders" is **English** on Overview, Wellness
  Score, Wellness Leagues but **German** ("Alle Länder" …) on Publish Notifications → different
  components/bundles; the English instances are the gap.
- Date preset "Dieser Monat" is **German** on Wellness Score/Leagues but **English** ("This
  Month") on Overview → inconsistent (Overview instance is the gap, Bug #2).

---

## Run 1 (cont.) — 2026-07-10 — German (de), Analyze → Reports (6 screens)

### Bug #19
[Copy/Functional - P3]
[Vantage Fit Admin — Reports (all) → filter default values]
Across all report screens the report filter default values render in English while their labels
(where shown) and the rest of the page are German. Recurring instance of the Bug #1 class,
specific to Reports.

Expected: "Alle Länder", "Alle Abteilungen", "Alle Altersgruppen", "Alle Geschlechter", and a German equivalent for "Enrolled"
Actual (English): "All Countries", "All Departments", "All Age Groups", "All Genders", "Enrolled" (Employee Report status filter)
Evidence: evidence/de/league-report_empty.png · evidence/de/employee-report.png · evidence/de/wellness-score-report.png

### Bug #20
[Copy - P3]
[Vantage Fit Admin — Reports → column-picker dropdown vs table headers]
The column-selector control lists column names in **English** while the rendered table column
headers for the same columns are **German** — an in-screen inconsistency on Employee,
Participant, and Redemption reports (and Wellness Leagues, Bug #18).

Expected: German column names in the picker, matching the headers (e.g. "Eintrittsdatum", "Transaktionsdatum", "Mitarbeiter-ID")
Actual: picker buttons "Date of Joining(+5 others)" (Employee/Participant), "Transaction Date(+10 others)" (Redemption), "Employee ID(+8 others)" (Wellness Leagues) — while headers show "Eintrittsdatum" / "Transaktionsdatum" / "Mitarbeiter-ID"
Evidence: evidence/de/employee-report.png · evidence/de/redemption-report.png

### Bug #21
[Copy - P3]
[Vantage Fit Admin — Reports → Wellness Score Report section]
On the Wellness Score Report the report-section title and its description render in English while
the page heading/subtitle/footer are German.

Expected: German (e.g. "Wellness-Scores der Mitarbeitenden" + German description)
Actual: "Employee Wellness Scores" (title); "Individual employee wellness score details" (description) — page heading "Wellness-Score-Bericht" is correctly German
Evidence: evidence/de/wellness-score-report.png

---

### Positive verification — Reports
- **Table column headers are fully German** on every report that renders them: Employee/Participant
  ("Name", "E-Mail", "Abteilung", "Land", "Eintrittsdatum", "Zuletzt aktiv"); Incentivisation
  ("Benutzer-E-Mail", "Challenge-Name", "Datum", "Grund", "Ländername", "Punkte", "Wert");
  Redemption ("Betrag", "Eingelöste Punkte", "Firmen-Benutzer-ID", "Mitarbeiter-ID", "Produktname",
  "Transaktionsdatum", "Währung"). **Incentivisation Report is fully German** (no English found).
- German empty/error states everywhere: "Keine Daten verfügbar. Passen Sie die Filter an und
  klicken Sie auf Generieren.", "Keine Einlösungsdaten für die ausgewählten Filter verfügbar.",
  "Exportieren", plus footer "Stimmt etwas nicht oder haben Sie ein Problem? Melden Sie es uns…".
  No layout breakage. Evidence: evidence/de/{league-report_empty,employee-report,participant-report,incentivisation-report,wellness-score-report,redemption-report}.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), Manage → Rewards + Configuration

### Bug #22
[Copy - P3]
[Vantage Fit Admin — Configuration → Add Employees → file-upload dropzone]
The file-upload dropzone label is English on Add Employees, while the equivalent control on
Upload Points is correctly German — inconsistent and a missing translation.

Expected: German, e.g. "Zum Hochladen klicken oder ziehen und ablegen" (as used on Upload Points)
Actual: "Click to upload or drag and drop"
Evidence: evidence/de/add-employees_form.png · evidence/de/upload-points_form.png (German equivalent)

### Bug #23  ⭕ BACKEND-DEFERRED (verified — not a Phase-1 FE defect; kept for visibility)
[Copy/Functional - P3 · BACKEND]
> Verified backend-sourced: the email-type titles + descriptions are served by `/vantagefit/api/dashboard/v1/email/getAll` as `title`/`description` fields. Page chrome around them is FE (German). Expected English until backend translation lands. Owned by the backend localization ticket.
[Vantage Fit Admin — Configuration → Preview Emails → email-type cards]
All 9 email-type card titles and their descriptions render in English while the page chrome
(heading, subtitle, "In neuem Tab öffnen", footer note) is German.

Expected: German titles + descriptions
Actual (English titles/descriptions): "Welcome Email (Add Employee)" / "Received when an employee is added to the system via Add Employee" · "Welcome Email (Invite to Challenge)" / "Received when a new user is invited to a challenge" · "Intro to App" / "Triggered when employee logs in to the app for the first time" · "Challenge Reminder" / "Reminds employees that the challenge is starting in 24 or 72 hours(sent twice)" · "Challenge Start" · "Weekly Summary" / "Weekly digest with challenges, wellness tier status, and upcoming events" · "Challenge Completion" · "Event Invite / RSVP Confirmation" · "Direct Message from HR"
Note/Doubt: The actual email template bodies are a separate/deferred email-localization scope, but these **admin-facing labels/descriptions** are FE chrome and should be localized.
Evidence: evidence/de/preview-emails.png

---

### Positive verification — Manage
- **Upload Points (/fit/reward-hub/upload-points): essentially fully German.** "Punkte in großen
  Mengen hochladen", "Verteilen Sie Prämienpunkte an Ihre Mitarbeiter", "Wallet auswählen", "Land
  auswählen", "Upload-Typ" (Primär / Anerkennungen / …), "Zu befolgende Schritte" with all steps
  German, dropzone "Zum Hochladen klicken oder ziehen und ablegen", "Vorschau"/"Absenden". Only
  "Reward" (a wallet name) stays English → likely BE/content, Note/Doubt. Evidence: evidence/de/upload-points_form.png
- **Settings (/fit/configuration/settings): fully localized, high quality.** "E-Mail-Einstellungen",
  "Challenge-Einstellungen", "App-Einstellungen" sections with all toggle labels + descriptions
  German (e.g. "Benutzer können Teams erstellen", "Maximale Teamgröße", "Prüfung beim Speichern
  mehrerer Aktivitäten"). **No separate UI-language setting exists here** → the left-rail
  dropdown is the only language control (partly resolves doubt N3). No layout breakage. Evidence: evidence/de/settings.png

---

## Run 1 (cont.) — 2026-07-10 — German (de), AC3 (fallback) + AC5 (persistence) + accessibility

### Bug #24
[Accessibility - P3]
[Vantage Fit Admin — global → `<html lang>` attribute]
The document language attribute is not updated when the UI language changes. With German
selected and the entire UI rendering in German, `document.documentElement.lang` is still `"en"`.
Screen readers will apply English pronunciation to German text.

Expected: `<html lang="de">` when German is selected (and matching codes for other languages)
Actual: `<html lang="en">` regardless of selected language
Verification: `browser_evaluate` → `htmlLang: "en"` while nav/content is German.
Evidence: (DOM attribute; see coverage-log AC notes)

---

### AC5 — Admin language preference persists across sessions → PASS (within browser); cross-device = Note/Doubt
- Preference stored in **`localStorage.fit_lang = "de"`**. It persisted across every in-run
  navigation and across a hard reload of `/fit/overview` (nav re-rendered German, switcher =
  German selected). localStorage is durable, so it also survives new tabs and browser restarts.
- **Note/Doubt (backend phase):** because the preference is browser-local (localStorage), it will
  **not** follow the admin to a different device/browser. If "across sessions" is meant to include
  cross-device, that requires server-side persistence of the user locale (not yet present). Full
  logout→login on the same browser is a recommended manual confirmation (localStorage typically
  survives logout unless explicitly cleared).

### AC3 — Fallback behaves correctly when a translation is missing → PASS (behavior); coverage target NOT met
- **Behavior PASS:** across all 25 screens, every untranslated string fell back to **clean
  English**. No raw i18n keys (`admin.fit.*`), no blanks, no `undefined`/`null`, no broken
  layout were observed anywhere. So the fallback mechanism itself behaves correctly.
- **VF-2206 coverage target NOT met:** the dev AC "No missing-translation fallbacks remain" is
  not satisfied — English fallbacks are widespread (Bugs #1–#23 document the specific gaps).
  These are coverage gaps, not fallback failures.

---

## ⚠️ FE/BE SOURCE VERIFICATION (2026-07-10) — READ BEFORE TRIAGING ABOVE BUGS

The bugs above were first classified by heuristic (static chrome = FE, dynamic data = BE). On
review I **verified each uncertain string's source** two ways: (1) inspecting the actual **API
response bodies** the app fetched, and (2) searching the loaded **FE JS bundles** for the literal
string. Method validated with controls: known API strings ("Multi Week Multi Activity", "Ends in
62 days", "Wellness Score by Department", "Welcome Email (Add Employee)") were **absent** from all
JS bundles, and known chrome strings were **present**.

**Rule:** string present in a JS bundle → **frontend**; string present in an API response and
absent from bundles → **backend** (expected English until backend translation lands — NOT a
Phase-1 FE bug).

### → RECLASSIFIED to BACKEND (remove from FE bug count; expected untranslated for now)
- **Bug #9** (challenge status "Ended" / "Ends in X days" / "Active"): backend — FE renders the
  API field `statusString` verbatim. *Nuance:* upcoming challenges show German "Bevorstehend"
  though the API sends `statusString:"Coming Soon"`, so a FE status-mapping layer exists but
  covers only some states — worth a dev note, but the ongoing/ended text is backend.
- **Bug #10** (challenge type "Multi Week Multi Activity" / "Race" / "Journey"): backend — FE
  renders API field `challengeTypeName` verbatim. (Source: `/vantagefit/api/v1/get/ongoingCampaigns`.)
- **Bug #17** (Wellness Score stat cards, chart titles, legends): backend — all served by
  `/vantagefit/api/dashboard/v1/workforce-health/wellness-score/insights/stream` as `header`/`label`
  fields. Only the page heading/subtitle (German) are FE. The mixed line "Current period breakdown •
  Gesamtpunktzahl: 43" = English API text + FE-added German word.
- **Bug #23** (Preview Emails: 9 email-type titles + descriptions): backend — served by
  `/vantagefit/api/dashboard/v1/email/getAll` as `title`/`description` fields.

### → CONFIRMED FRONTEND (valid Phase-1 bugs — string found hardcoded in JS bundles)
- **#1** "All Countries", **#2** "This Month", **#6** challenge-type cards ("Custom Challenge",
  "Race Challenge", "Journey Challenge" + descriptions), **#12** "Create content" modal
  ("What would you like to create"), **#15** Announcements page ("What is an Announcement",
  "Existing Announcements", "Search by title", …). Also FE by other evidence: **#13** (same "All"
  control as #1), **#22** (German equivalent exists on Upload Points), **#24** (`<html lang>` DOM
  attribute), **#7/#8** (German i18n string / badge chrome), **#4** (switcher UI).

### → STILL UNCERTAIN (could be backend-supplied metadata; dev to confirm before fixing FE)
- **Bug #16** (Email Designer "Rich Email Composer" dialog): not in currently-loaded bundles, but
  its chunk lazy-loads on dialog open so this is inconclusive. Content is static product/marketing
  copy → **most likely FE**, but not bundle-verified in this pass.
- **Bug #18** (Wellness Leagues "Based on avg daily steps over 21 days"; column "Employee ID"):
  not verified either way.
- **Bug #20** (report column-**picker** names "Date of Joining" / "Transaction Date" while table
  headers are German): the German headers prove FE i18n works for headers; the picker likely pulls
  raw **backend column metadata** — plausibly BE. Confirm with dev which source the picker uses.
- **Bug #21** (Wellness Score Report section "Employee Wellness Scores" / description): may come
  from the `wellness-score/employee-report` API (BE) or be a FE section header. Unverified.
- **Bug #14** ("Article" in Content Library table): the content record's `type` value may be
  backend data; "Artikel" appears elsewhere (FE has the mapping). Confirm whether the table shows
  a raw BE value or a FE-mapped one.
- **Bug #3 / #11** (English date format "Jul 01, 2026"): date formatting is normally FE (`Intl` +
  locale), but confirm the API doesn't send pre-formatted date strings.

### Net effect on AC2
Still **FAIL** — genuine FE untranslated strings remain (#1, #2, #6, #12, #13, #15, #22, plus
formatting/#7/#8 and likely #16). **But** the FE localization is **more complete than the raw
list implied**: several headline "whole page/section in English" findings (#17 Wellness Score,
#23 email types) and the challenge status/type text (#9, #10) are **backend content**, not FE
gaps. The remaining FE gaps are specific hardcoded strings/screens plus formatting, not the
analytics/list bodies.

---

## Run 2 — 2026-07-10 — Spanish (es) — targeted cross-language pass

**Scope:** targeted pass leveraging Run 1's structural map — confirmed language-agnostic FE bugs
recur, checked Spanish translation quality/register, and Spanish-specific issues. Not all 25
screens re-walked at full depth. Evidence in `evidence/es/`.

### AC1 (Spanish) — PASS
Switching to Spanish re-renders the whole UI. Nav fluent and accurate with correct accents:
"Resumen", "Crear desafío", "Desafíos activos/anteriores", "Programas", "Comunidad", "Salud del
personal", "Análisis de salud", "Puntuación de bienestar", "Informes", "Configuración", "Ajustes",
"Añadir empleados", "Contactar al gerente de cuenta". Widget "Idioma" / "Idioma del contenido" /
"Licencias". No German bleed, no raw keys.

### Bug #25
[Copy/UX - P3]
[Vantage Fit Admin — Spanish → verb register (app-wide)]
Spanish uses the **informal "tú"** imperative throughout, whereas German uses the **formal
"Sie"**. For an enterprise admin product, formal **"usted"** is the usual expectation, and the
cross-language register is now inconsistent.

Expected (formal usted): "Empiece a crear…", "Cree sus propios…", "Envíe…", "Ingrese…"
Actual (informal tú): "Empieza a crear desafíos…", "Crea tus propios…" (Create Challenge); "Envía notificaciones…", "Ingresa el título…" (Publish Notifications)
Note/Doubt: Register is a style-guide decision — tú is not wrong, but it should be (a) intentional and (b) consistent with the other languages. Needs native-speaker + style-guide sign-off. Logged as a call-out rather than a hard defect.
Evidence: evidence/es/create-challenge_landing.png · evidence/es/publish-notifications_form.png

### Language-agnostic FE bugs — CONFIRMED recur identically in Spanish
These render **English in both German and Spanish**, proving they are un-externalized FE strings
(not per-language translation gaps):
- **#1** "All Countries", **#2** "This Month", **#8** "NEW"/"FREE" badges — English in es (Overview).
- **#6** challenge-type cards ("Custom Challenge", "Race Challenge", "Journey Challenge",
  "E-Marathon", "Streak Challenge" + descriptions) — English in es (Create Challenge).
- **#7** capitalization — "Crea tus propios **Nuevos** desafíos" ("Nuevos" wrongly capitalized),
  same defect as German → the source i18n string carries the error into every language.
- **#12** Create-content modal, **#15** Announcements page — full English in es (identical to de).
  Announcements verified English verbatim: "Announcements", "What is an Announcement?", "Existing
  Announcements", "Search by title...", "Delete announcement", "Show more".

### Backend strings (es) — still English, as expected
Template names/descriptions (Stress Free Month, "Join our HR team…") English in es — BE content,
deferred (same as de).

### Positive verification (Spanish)
- **Nav + Overview chrome, Create Challenge instructional copy, Publish Notifications form** all
  cleanly Spanish with correct accents (Desafíos, Añadir, Puntuación, Título). Filter defaults on
  Publish Notifications correctly Spanish ("Todos los países / departamentos / géneros / grupos de
  edad") — confirming the Overview "All Countries" (#1) is a specific component gap, not a global
  miss. No layout breakage observed. "NUEVO" template badge localized (like de "NEU").
- **¿ ¡ inverted punctuation:** could not be exercised — the translated UI checked had no
  interrogative/exclamatory sentences (the one question, "What is an Announcement?", is untranslated
  English). Flag for a screen with Spanish questions/validation messages.
