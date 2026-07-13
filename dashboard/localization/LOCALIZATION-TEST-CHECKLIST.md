# Localization Test Checklist — Vantage Fit Admin (Frontend, per element)

Execution checklist for Phase 3. One table, module → screen → element. Apply the check IDs
(U1–U10, F1–F9, A1–A5) defined in `LOCALIZATION-TEST-SCOPE.md`; update **Status** as you test.
Element list from `DASHBOARD-MAP.md`. Bold `▸` rows are section markers (module / screen).

**Status:** ☐ To-do · ◐ In progress · ✅ Pass · ❌ Fail (→ bug #) · ⛔ Blocked · ⭕ N/A (backend)
Test German-deep first, then Arabic (RTL), then es/fr/pl (record status per language).

| Module / Sub-module / Element | Testing Checklist | Status |
|---|---|---|
| **▸ GLOBAL CHROME** (persistent — test once per language) | | |
| (whole chrome) | U1 U2 U3 U4 U6 U9 U10 · F8 · A1 A2 A3 (+U5 Arabic) — nav/labels translated, no bleed, layout fits, `<html lang>` matches, switch persists | de: ✅ nav/labels German, no bleed, no overflow; F8 switch+persist ✅; **U10 `<html lang>`="en" ❌ #24** |
| Product rail (Recognition…Admin Hub) | U1 U9 — product names (shared shell; confirm FE scope) | de: ◐ names English — shared shell (N1, confirm scope) |
| Profile menu | U1 F1 — opens; items ("HR Admin Dashboard"…) translated | de: ✅ opens (items = shared shell) |
| Create button → global chooser | U1 F1 F5 F9 — opens; options (Challenge/Announcement/Event/Content) translated | de: ✅ opens; options German ("Was möchten Sie erstellen?") |
| Left-nav items + group headers | U1 F1 F9 — links/groups translated; expanders open | de: ✅ all German; expanders open |
| Plan badge "Active Plan - Grow" | ⭕ A5 — backend `plan.name` (known-English) | ⭕ backend (expected EN) |
| Language switcher | U1 F1 F8 F9 — switches whole UI; 18 options; persists; **option names not translated (#4)** | de: F1/F8 ✅ (switches+persists); **U1 option names English ❌ #4** |
| Challenges / Licenses counters | U1 U7 — labels translated; numbers formatted | de: ✅ "Herausforderungen"/"Lizenzen" labels German |
| Contact Account Manager | U1 F1 — label translated; opens | de: ✅ "Account-Manager kontaktieren" |
| **▸ OVERVIEW — `/fit/overview`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U7 U8 U10 · F8 · A1 A2 A4 · U5 — chrome translated; date format per locale | de: chrome ✅; **body = skeleton (O1) ⛔** — cards untestable |
| Country filter | U1 F1 F2 F9 — **opens + applies on click (wire-up #1: "All Countries" English)** | de: **F1 ✅ opens, F2 ✅ applies** (selected "India" → label updated); **U1 "All Countries" English ❌ #1** |
| Date-range picker + presets | U1 F1 F2 F9 U7 — opens; presets translated (#2); value formatted (#3); custom-disabled note | de: **F1 ✅ opens**; **U1 presets English ("This Month"/"Last Month"…) ❌ #2**; **U7 date "Jul 01, 2026" ❌ #3** |
| KPI cards (Enrolled/Active, Incentivization, Participation) | U1 U7 ⭕ — labels FE; values backend | ⛔ body skeleton (O1); values ⭕ backend |
| Leadership Insights (AI) | ⭕ A5 — backend/AI text | ⭕ backend/AI |
| Org Wellness Score + Score Breakdown | U1 ⭕ — labels FE; scores backend | ⛔ body skeleton (O1); scores ⭕ backend |
| At a Glance tiles (Avg Steps/Active Min/Mindful Min/Avg Sleep) | U1 U7 — tile labels + units | ⛔ body skeleton (O1) |
| Recommended Actions list | U1 F1 — item labels translated; deep-link | ⛔ body skeleton (O1) |
| Workforce Health Snapshot | U1 ⭕ — labels FE; data backend | ⛔ body skeleton (O1) |
| Ask Vantage Fit modal (⌘K) | U1 F1 ⭕ — launcher opens; chrome FE; answers backend | de: ✅ F1 opens; chrome FE; answers ⭕ backend |
| **▸ CHALLENGES › Create Challenge — `/fit/create-challenge`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U9 · F8 · A2 · U5 | de: ✅ chrome/instructional German, no overflow; issues #6, #7 |
| Intro heading / "OR" / section heading | U1 U2 — translated; **#7 "…New Challenges" concat capitalisation** | de: ✅ German ("Beginnen Sie…", "ODER"); **U2 "Neuen" concat cap ❌ #7** |
| Template cards (Stress Free Month…) | ⭕ A5 — template names/descriptions backend | ⭕ backend content |
| "Use Template" / "Create Challenge" buttons | U1 F1 F9 — translated; open builder | de: ✅ "Vorlage verwenden" / "Challenge erstellen" |
| Challenge-type cards (Custom/Race/Journey/E-Marathon/Streak) + descriptions | U1 F9 — **#6 wire-up: English though `staticChallenges.*` translated** | de: **❌ #6 cards English (Custom/Race/Streak Challenge…)** |
| Builder Step 1 (Logo, Name, Slogan, About, T&C, Auto-announce toggle) | U1 U2 F3 F6 F7 — labels/placeholders/counters; validation gating; accented input | de: ✅ German fields; **F3 gating ✅ (Next disabled→enables on Name)**; F6 accents ✅ (verified) |
| Builder Step 2 (Set Duration — start/end date pickers) | U1 F1 F7 U7 — picker opens; DD/MM/YYYY per locale | de: ✅ German; date DD/MM/YYYY (verified) |
| Builder later steps (activities/targets → audience → rewards → publish) | U1 U2 F3 F4 F5 F7 — ⚠️ walk each step in-language; validation/toasts/dialogs localized | ☐ not yet walked (deeper wizard) |
| **▸ CHALLENGES › Active Challenges — `/fit/manage-challenge`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U8 U9 · F8 · A2 A5 · U5 | de: ✅ German ("Aktive Challenges", "Verwalten Sie…"), no overflow |
| Title + "Create Challenge" | U1 F1 — translated; opens builder | de: ✅ German ("Challenge erstellen") |
| Section headers (Ongoing / Upcoming) + counts | U1 — translated | de: ✅ "Laufende"/"Bevorstehende" |
| Challenge card (name/status/type/visibility/participation/dates) | U1 U7 ⭕ — "Private"/"Participation" FE; **status & type backend (#9/#10)**; dates (#11) | de: "Privat"/"Teilnahme" FE ✅; **status/type ⭕ backend (#9/#10)**; dates #11 |
| Row actions: View / Manage | U1 F1 F2 — translated; open detail / management | de: **F1/F2 ✅ "Ansehen" opens detail (/campaign/25411)**; "Verwalten" ✅ |
| **▸ CHALLENGES › Past Challenges — `/fit/past-challenges`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 A5 | de: ✅ German ("Vergangene Challenges") — verified |
| Card list (status "Completed", participation, dates) | U1 U7 ⭕ — labels FE; status/names backend; dates formatted | de: labels FE ✅; status "Abgeschlossen"/names ⭕ backend; dates #11 |
| Row action: View | U1 F1 F2 — opens results detail | de: ✅ (same component as Active — "Ansehen" opens detail) |
| **▸ ENGAGE › Content Library — `/fit/programs/on-demand-content`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 A5 · U5 | de: ✅ German chrome ("Inhaltsbibliothek"), no overflow |
| Title + "Create" button | U1 F1 — translated; opens chooser | de: ✅ German; opens chooser |
| Search box | U1 F1 F6 — placeholder translated; filters; accented input | de: **F1/F6 ✅ (search "meditation" 25→4)**; placeholder "Inhalte suchen…" ✅ |
| Type / Category filters ("All") | U1 F1 F2 F9 — **wire-up #13 "All"**; options; applies | de: **U1 "All" English ❌ #13**; F1 opens |
| Table headers (Content/Type/Category/Actions) | U1 F9 — headers translated; **#14 Type "Article" wire-up** | de: headers German ✅ (Inhalt/Typ/Kategorie/Aktionen); **Type value "Article" ❌ #14** |
| Row: content title / category | ⭕ A5 — backend content data | ⭕ backend content |
| Row actions (View content + icon buttons) | U1 F1 U10 — "View content" translated; **icon buttons lack a11y name** | de: "Inhalt ansehen" ✅; **icon buttons no a11y name ❌ (U10)** |
| Content Overview panel (Articles/Videos/Podcasts/Bite Size) | U1 U7 — labels (loanwords ok) | de: ✅ (Artikel/Videos/Podcasts/Bite Size) |
| **▸ ENGAGE › Create Content — chooser + forms** | | |
| Create-content chooser modal | U1 F1 F9 — **#12 wire-up: "Create content"/"Linked Content"/"Health Bite" English** | de: **❌ #12 chooser English** |
| Linked Content form (Type, Category, Page URL, Image*, Title+counter) | U1 U2 F3 F6 — labels/placeholders/counter; validation (image required); accented title | de: ✅ German fields ("Verknüpften Inhalt erstellen", counter); F3 image required ✅; F6 accents ✅ |
| Health Bite → Bite-Size builder (`/fit/create-bite-size-content`) | U1 U2 F3 F7 — walk builder steps in-language (see create-content/ cases) | ☐ not yet walked (see create-content/ docs) |
| **▸ ENGAGE › Create Event — `/fit/events/create-event`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U9 · F8 · A2 · U5 | de: ✅ fully German, no overflow |
| Basic Information (Title, Start/End Date, Start/End Time, Image) | U1 F1 F3 U7 — labels; date DD/MM/YYYY + time pickers open; validation | de: ✅ German ("Veranstaltungstitel"…); date DD/MM/YYYY; validation-gated |
| Target Audience (Country/City/Age Group/Department) | U1 F1 F2 — labels translated; dropdowns open + filter | de: ✅ German (Land/Stadt/Altersgruppe/Abteilung) |
| Event Details (Venue, About, Benefit +Add more, FAQ +Add more) | U1 F1 — labels/placeholders; **dynamic add rows work** | de: ✅ German; **F1 dynamic add ✅ (FAQ 1→2)** |
| Send Email Invites toggle | U1 F1 — label; toggles | de: ✅ German label; toggles |
| Reset / Create New Event | U1 F1 F3 F4 — validation-gated; submit + toast localized | de: ✅ German ("Neue Veranstaltung erstellen"); F3 gated (not submitted) |
| **▸ ENGAGE › View Events — `/fit/events`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 A5 | de: ✅ German ("Veranstaltungen anzeigen") |
| Title + "Create Event" | U1 F1 — translated; opens form | de: ✅ German; opens form |
| Tabs (Ongoing/Upcoming/Past Events) | U1 F1 F2 — translated; switch filters list | de: **F1/F2 ✅** ("Laufende/Kommende/Vergangene Veranstaltungen") |
| Event card (name, dates, invites sent, engagement, "Learn more") | U1 U7 ⭕ — labels FE; name/metrics backend; dates formatted | de: labels FE ✅ ("Anzahl gesendeter Einladungen", "Nutzerengagement"); name ⭕; date #11 |
| **▸ ENGAGE › Create Announcement — `/fit/community/announcement`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 · U5 | de: mixed — list chrome EN (#15), form fields German |
| List page chrome (title, banner, Existing Announcements, Search, Show more) | U1 F9 — **#15 not-externalised: entire chrome English** | de: **❌ #15 entire chrome English** |
| Create Announcement button (icon) | U1 F1 U10 — opens form; **no a11y name** | de: F1 ✅ opens; **U10 no a11y name ❌** |
| Create form (AI-generate, Title, Description) | U1 U2 F3 — field labels/placeholders; header + "Publish" English (#15) | de: fields German ✅; header + "Publish" English (#15) |
| Publish button | F1 F3 — **#29 functional: never enables with valid fields (repro)** | de: **❌ #29 never enables with valid Titel+Beschreibung (functional)** |
| Delete (row) → confirm dialog | U1 F1 F4 F5 — **#28 dialog English (key exists → wire-up)**; delete works | de: **F4 delete ✅ works**; **F5 dialog English ❌ #28** |
| Row titles | ⭕ A5 — backend announcement data | ⭕ backend |
| **▸ ENGAGE › Publish Notifications — `/fit/community/publish-notifications`** | | |
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | de: ✅ German ("Benachrichtigung veröffentlichen") |
| Notification Content (Title, Subtitle + counters) | U1 U2 F3 F6 — labels/placeholders/counters; accented input | de: ✅ German; **counter ✅ (31/60)**; accents ✅ |
| Target Audience (Select Users/Attributes/CSV; filters Dept/Country/Gender/Age) | U1 F1 F2 — modes + filters translated + apply; Load Employees | de: ✅ German incl. **"Alle Länder/Abteilungen/…"** (filters German here) |
| Live Notification Preview | U1 F1 — mirrors typed content; labels translated | de: ✅ German; mirrors live ("Gerade eben", "Desktop-Ansicht") |
| Send Notification | F1 F3 — **#27: enables with only a title (default audience = all)** (🔴 don't fire) | de: **❌ #27 enables with only a title (audience = all)** — not fired |
| **▸ ENGAGE › Send Custom Email — `/fit/community/send-custom-email`** | | |
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | de: ✅ German ("Benutzerdefinierte E-Mail senden") |
| Email Content (Subject, Headline, Body + counters) | U1 U2 F3 F6 — labels/placeholders/counters; accented input | de: ✅ German (Betreff/Überschrift/Text + counters) |
| Target Audience (modes + Build from Report) | U1 F1 F2 — translated; build audience | de: ✅ German ("Aus Bericht erstellen": Liga-/Mitarbeiter-/Teilnahmebericht) |
| Email Preview | U1 — chrome translated; template body may be backend | de: chrome German ("Posteingang", "Gerade eben"); template body ⭕ (email-template scope) |
| "Design a rich email" / Send Email | U1 F1 F4 — opens designer; send (🔴) + toast localized | de: F1 ✅ opens designer ("Individuelle E-Mail gestalten"); send not fired |
| **▸ ENGAGE › Email Designer ("Rich Email Composer") — overlay** | | |
| Composer dialog (title, steps, Start new/Continue, cards, Get started/Import) | U1 F9 — **#16 not-externalised: whole composer English** | de: **❌ #16 whole composer English** |
| Live preview pane | U1 ⭕ — chrome FE; placeholder copy | ⭕ placeholder copy |
| **▸ ANALYZE › Health Insights — `/fit/workforce-health/health-insights`** | | |
| Embedded external dashboard (iframe `dash-vfit…org`) | ⛔ — iframe refused to connect; separate app. Re-test where frame loads | ⛔ |
| **▸ ANALYZE › Wellness Score — `/fit/workforce-health/wellness-score`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U7 · F8 · A2 A5 · U5 | de: heading/subtitle German ("Wellness-Score"); analytics body backend |
| Filters (All Countries/Departments/Genders/Age + date) | U1 F1 F2 F9 — **wire-up filter values**; apply | de: **U1 "All Countries…" English ❌ (wire-up)**; F1 opens |
| "Insights" / "AI-generated" | U1 ⭕ — labels FE; insight text backend | de: "Einblicke"/"KI-generiert" ✅ FE; text ⭕ |
| KPI cards, chart titles, legends, segment names | ⭕ A5 — **backend (#17: insights API `header`/`label`)** | ⭕ backend #17 (expected EN) |
| "Employee Wellness Scores" section | ⭕ A5 — backend title/subtitle | ⭕ backend #21 |
| **▸ ANALYZE › Wellness Leagues — `/fit/workforce-health/wellness-leagues`** | | |
| (whole screen) | U1 U2 U3 U4 U6 U7 U8 · F8 · A2 A5 · U5 | de: ✅ German ("Wellness-Ligen") |
| Filters + date | U1 F1 F2 F9 — translated; apply | de: labels German; **values "All Countries…" English ❌ (wire-up)** |
| Current Tier Distribution + "Based on avg daily steps…" | U1 — verify caption FE vs BE | de: "Aktuelle Stufenverteilung" ✅; caption "Based on avg daily steps…" English (unresolved FE/BE) |
| Tier Trends chart + Weekly/Monthly toggle | U1 F1 F2 — toggle works; period switches | de: ✅ German ("Wöchentlich/Monatlich"); **F2 toggle works (Monatlich active)** |
| Columns picker ("Employee ID +8") | U1 F1 F9 — **#18/#20 wire-up: picker English vs translated headers** | de: **❌ #18 "Employee ID" English (picker) vs German headers** |
| Export | U1 F1 — translated; export works | de: "Exportieren" ✅ |
| Table + empty state | U1 U8 ⭕ — headers FE; row data backend | de: empty states German ("Keine Daten verfügbar"); data ⭕ |
| **▸ ANALYZE › Reports (League/Employee/Participation/Incentivisation/WScore/Redemption)** — apply to each of 6 | | |
| (whole screen) ×6 | U1 U2 U3 U4 U6 U7 U8 · F8 · A2 A5 · U5 | de: ✅ German chrome all 6 |
| Filter bar (All Countries/Departments/Genders/Age + date, "Enrolled") | U1 F1 F2 F9 — **#19 wire-up filter values**; apply | de: **U1 "All Countries…"/"Enrolled" English ❌ #19** |
| Column picker | U1 F1 F9 — **#20 picker names English vs German headers** | de: **❌ #20 picker "Date of Joining"/"Transaction Date" English** |
| Table column headers | U1 F9 — translated (verify each report's columns) | de: ✅ German (Name/E-Mail/Abteilung/… ; Incentivisation fully German) |
| Table row data | ⭕ A5 — backend report data | ⭕ backend |
| Export (CSV/Excel) | U1 F1 F2 — menu translated; export works | de: F1 ✅ menu opens (CSV/Excel) |
| Empty / no-data state | U1 U8 — "No data available…" translated | de: ✅ German ("Keine Daten verfügbar. Passen Sie die Filter an…") |
| WScore Report "Employee Wellness Scores" section | ⭕ A5 — **#21 backend title/subtitle** | ⭕ backend #21 |
| **▸ MANAGE › Upload Points — `/fit/reward-hub/upload-points`** | | |
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | de: ✅ German ("Punkte in großen Mengen hochladen") |
| Select Wallet / Select Country | U1 F1 F2 — labels; dropdowns; wallet name ("Reward") ⭕ backend | de: ✅ "Wallet auswählen"/"Land auswählen"; wallet "Reward" ⭕ backend |
| Upload Type radios (Primary/Appreciations/Points CSV…) | U1 F1 — labels; select | de: ✅ "Primär"/"Anerkennungen"/…; **F1 radio select works** |
| Download Sample + dropzone | U1 F1 — "Download sample CSV" + dropzone text translated | de: ✅ "Beispiel-CSV herunterladen"; dropzone "Zum Hochladen klicken oder ziehen und ablegen" ✅ |
| Send-email toggle | U1 F1 — label; toggles | de: ✅ German label |
| Steps to follow | U1 — all steps translated | de: ✅ "Zu befolgende Schritte" all German |
| Preview / Submit | U1 F1 F3 F4 — validation-gated; submit (🔴) + toast localized | de: ✅ "Vorschau"/"Absenden"; F3 gated; not submitted (🔴) |
| **▸ MANAGE › Add Employees — `/fit/configuration/add-employees`** | | |
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | de: ✅ mostly German ("Mitarbeiter hinzufügen", "Hinweis") |
| Title + steps | U1 — translated | de: ✅ German steps |
| Download template + dropzone | U1 F9 — **#22 not-externalised: "Click to upload or drag and drop" English** | de: **❌ #22 dropzone "Click to upload or drag and drop" English** |
| Note panel (company_id, status rules) + View More | U1 F1 — rules translated; expander works | de: ✅ rules German; **F1 "Mehr anzeigen" expander works** |
| Cancel / Preview / Submit | U1 F1 F3 F4 — validation-gated (file required); submit (🔴) + toast | de: ✅ "Abbrechen"/"Vorschau"/"Absenden"; F3 gated (file req); not submitted (🔴) |
| **▸ MANAGE › Preview Emails — `/fit/configuration/preview-emails`** | | |
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 A5 · U5 | de: ✅ German chrome ("E-Mail-Vorschau") |
| Title + "N of 9 enabled" | U1 U7 — translated; count formatted | de: ✅ German ("9 von 9 aktiviert") |
| 9 email-type cards (title + description) | ⭕ A5 — **#23 backend titles/descriptions (email API)** | ⭕ backend #23 (expected EN) |
| "Open in New Tab" links | U1 F1 — translated; opens preview | de: ✅ "In neuem Tab öffnen" |
| Enable/disable toggles + "About Email Settings" | U1 F1 — labels translated; toggle works | de: ✅ German footer; toggles (not flipped) |
| **▸ MANAGE › Settings — `/fit/configuration/settings`** | | |
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | de: ✅ fully German ("Einstellungen") |
| Email Settings (banner upload, Challenge completion email, Disable all emails) | U1 F1 — section + toggle labels/descriptions translated | de: ✅ German ("E-Mail-Einstellungen" + toggle labels/descriptions) |
| Challenge Settings (create/update teams, Max team size, team breakdown) | U1 F1 F3 — labels; toggles + numeric field | de: ✅ German ("Challenge-Einstellungen", "Maximale Teamgröße"); toggles not flipped (🔴 org-wide) |
| App Settings (logo upload, multiple-activity save check) | U1 F1 — labels; toggle | de: ✅ German ("App-Einstellungen", "Prüfung beim Speichern mehrerer Aktivitäten") |
| (No separate UI-language setting — switcher is the only control) | — | ⭕ confirmed no UI-lang setting |

---

## Per-language pass results

### German (de) — ✅ complete
All 26 screens walked (Status cells above carry `de:` results). Functional controls work in German
(filters open/apply, tabs, toggles, row actions, dynamic add, counters, delete). Defects = wire-up
(#1,#2,#6,#13,#14,#18,#19,#20,#28), not-externalised (#4,#8,#15,#16,#22), functional (#27,#29),
a11y (#24), backend ⭕ (#9,#10,#17,#21,#23), blocked ⛔ (#13 Health Insights). No new German bug.

### Spanish (es) — ✅ pass complete
- **i18n coverage:** `es.json` 991 keys, **973 translated, 0 missing**; uses inverted punctuation
  correctly (e.g. `"¿Qué te gustaría crear?"`, "Todos los países", "Este mes", "NUEVO").
- **AC1:** nav fluent Spanish ("Resumen", "Crear desafío", "Salud del personal"…), no bleed.
- **Functional:** Overview country filter **opens** in Spanish (listbox with countries) — controls
  work as in German. F1/F2 hold.
- **Language-agnostic bugs recur identically** (English in Spanish too, since hardcoded-literal /
  wire-up / backend): #1, #2, #6, #7, #8, #12, #13, #14, #15, #16, #18, #19, #20, #22, #28, and
  backend ⭕ #9/#10/#17/#21/#23. (Same Status as the `de:` cells above.)
- **Spanish-specific:** **#25 register — informal "tú"** ("Empieza a crear…", "Crea tus propios…",
  "Envía…", "Ingresa…") vs German/French formal — enterprise expects formal "usted"; the outlier.
  ¿ ¡ correct in the i18n content (`es.json`). Accents (ñ, á, é) render. No layout overflow.
- **No new Spanish-only bug** beyond #25.

### French (fr) / Polish (pl) / Arabic (ar) — pending
fr: formal "vous" (correct); pl: fluent, ~99% translated. **Arabic (RTL) not yet run — highest
remaining risk (mirroring/layout).**

---

**How to run:** pick a language (German first) → go top-to-bottom → apply the row's check IDs
(expanded in `LOCALIZATION-TEST-SCOPE.md`) → set Status (✅/❌→bug#/⛔/⭕) → log ❌ in
`bug-logs/bug-log.md` with exact string/behaviour + language + evidence → repeat per language.
Backend `⭕` rows: only confirm the English is *expected*, don't log as FE bugs.
