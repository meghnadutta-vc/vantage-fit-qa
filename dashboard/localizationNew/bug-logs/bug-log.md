# Vantage Fit — Dashboard Localization (New) — Consolidated Bug Log

**Engagement:** Vantage Fit admin dashboard frontend localization · India tenant (`dashboard-v2.vantagecircle.co.in`, company 355, UAT)
**Languages:** German (deep) · English (baseline) · French + Spanish (dictionary-parity + spot-checks — dicts 991/991 complete, all bugs reproduce)
**Compiled:** 2026-07-21 · Per-module detail lives in the sibling `bug-logs/<module>.md` files (source of truth).

> **Classification tags**
> - **[FE]** — Frontend bug. Fixable in the web app: either a *wire-up gap* (a German/fr/es translation
>   EXISTS in `/assets/i18n/fit/*.json` but the component renders an English literal / wrong key) or
>   *not-externalised* (string hardcoded in English, no i18n key in any language) or a *formatting/layout*
>   issue (locale-unaware date/number formatter, truncation).
> - **[BE]** — Backend / data-driven. String comes from an API/master-list/template; stays English until a
>   backend-localization phase. Not a frontend defect.
> - **[FE/BE TBD]** — Source not yet proven; needs a FE-vs-BE triage (or product confirmation) before an owner
>   can be assigned.
>
> Most defects are **[FE]** (wire-up gaps against an otherwise-complete dictionary). See the classification
> index at the bottom.

---

## Priority summary (module-specific bugs)

| Priority | Count | IDs |
|---|---|---|
| **P1** | 0 | — |
| **P2** | 13 | Overview #1, #2 · CC#1 · RPT#1, #2 · CL#1 · EV#1 · CRC#1, #2 · ANN#1, #2 · ED#1 · WS#1 |
| **P3** | 16 | Overview #3, #5, #6, #7 · CC#2, #3, #4, #5 · MGC#1, #2 · RPT#3, #4, #5 · CL#2, #3 · EV#2 · WL#1 · AE#1 · PE#1 · SCE#1 · FR#1 *(see note — 21 rows incl. TBD/non-loc)* |
| **P4** | 4 | SET#1, SET#2 · CL#4, CL#5 |

Clean modules (0 bugs): **Past Challenges · Settings · Publish Notifications · Upload Points**.
Blocked: **Health Insights** (external analytics iframe — not localizable in-dashboard).

**Dynamic-flow pass (2026-07-22, `test-cases/dynamic-flows.md`):** validation is preventive everywhere
(disabled-submit + maxlength — no error strings). Success toasts localize in Publish Notifications
("Benachrichtigung an 1 Benutzer gesendet.") + Send Custom Email ("E-Mail an 1 Benutzer gesendet."), but
Announcements delete dialog + toast are English (**ANN#3**). Deferred (need disposable data / org-wide):
Announcement publish toast, Preview-Emails save toast, Create Event/Content + Upload-Points/Add-Employees
toasts.
**Run 2 (2026-07-22):** nav/redirect sweep 24/24 PASS. Create Content create toast + Preview-Emails save
toast LOCALIZED. 2 new mixed-language bugs: **DF#1** generic loading toast English; **UP#1** Upload-Points
"Preview" modal title English. Still deferred: Add Employees + Create Event create toasts, Announcement
publish. Test data left for cleanup: 1 Linked-Content item "QA localization test — please delete".

---

## Module-wise bug list

### Overview — `bug-logs/overview.md`
- **#1 · P2 · [FE]** — Main Overview content stays English in de/fr/es (stat cards, Org Wellness Score, Score Breakdown, At-a-Glance, Recommended Actions, Workforce Snapshot, Wellness Tiers, Active Challenges). Wire-up gap for keyed strings; a few sub-strings not-externalised.
- **#2 · P2 · [FE]** — Country filter default "All Countries" never translated (`targetAudience.filtersAll.country` exists).
- **#3 · P3 · [FE]** — Inconsistent localization on one screen ("View More" / "vs Prev period" English vs localized siblings).
- **#5 · P3 · [FE]** — Date range not locale-formatted ("Jun 21, 2026") — no locale-aware formatter.
- **#6 · P3 · [FE/BE TBD]** — Numbers/percentages ("23.7%") and currency ("$0") not locale-formatted; some values come from `overview/home/stream` — confirm FE vs API; currency choice may be intentional (product).
- **#7 · P3 · [FE]** — In-place language switch leaves stale strings until reload (change-detection/non-reactive binding).
- **#4 · P3 · [FE]** — a11y: `<html lang>` stays "en" + icon aria-labels English after switching. **(Cross-module — reproduces on every page.)**

### Create Challenge — `bug-logs/create-challenge.md`
- **CC#1 · P2 · [FE]** — 5 challenge-type cards (Custom/Race/Journey/E-Marathon/Streak) titles+descriptions English though `staticChallenges.*` translations exist.
- **CC#2 · P3 · [FE]** — Date-picker calendar not localized (English month/weekday names). **(Cross-module — same calendar in Reports/Events.)**
- **CC#3 · P3 · [FE]** — Audience filter operator "is in" English (query-builder operator not wired).
- **CC#4 · P3 · [FE/BE TBD]** — ~24 activity/task-type names English (Steps, Water Intake, Yoga Session…); likely a backend activities master list — triage.
- **CC#5 · P3 · [FE]** — Review/detail date values (English month names), "Week n" (hardcoded literal), "Custom Image" (not externalised).

### Manage Challenges — `bug-logs/manage-challenge.md`
- **MGC#1 · P3 · [FE/BE TBD]** — Card countdown "Ends In X Days" English; no i18n key found → backend `statusString` or hardcoded FE literal (triage).
- **MGC#2 · P3 · [FE — non-localization]** — "Ask Vantage Fit" chatbot overlay intercepts the "Update Challenge" click (UI/z-index; found incidentally).

### Past Challenges — `bug-logs/past-challenges.md`
- **CLEAN** — 0 module bugs (chrome + "Abgeschlossen"/"Privat" localize). Cross-module carry-overs only.

### Reports (all 6) — `bug-logs/reports.md`
- **RPT#1 · P2 · [FE]** — Report filter defaults English ("All Countries/Departments/Genders/Age Groups", "Enrolled", "Active Users") on all 6. **(Shared report-filter — also hits Wellness Score + Wellness Leagues.)**
- **RPT#2 · P2 · [FE]** — Column-selector control fully English (button, options, "N selected", "All") while the headers it controls are localized. **(Also on Wellness Leagues.)**
- **RPT#3 · P3 · [FE]** — WSR "Employee Wellness Scores" section title+subtitle English (not externalised/wired).
- **RPT#4 · P3 · [FE]** — Date values not locale-formatted + 3 inconsistent formats on one page + English calendar (same root as Overview #5 / CC#2).
- **RPT#5 · P3 · [FE/BE TBD]** — Currency renders as code+integer ("INR 25", "USD 1"); needs product confirmation whether code-prefix is intended vs locale currency formatting.

### Configuration → Settings — `bug-logs/settings.md`
- **CLEAN** — 0 defects. Observations only:
- **SET#1 · P4 · [FE/BE TBD]** — Content-language dropdown lists options in English regardless of UI language (endonyms-vs-English is a product/design call). Global sidebar element.
- **SET#2 · P4 · [FE]** — "Max team size" info icon has no accessible label (hover-only tooltip).

### Programs → Content Library — `bug-logs/content-library.md`
- **CL#1 · P2 · [FE]** — Content-type labels English in the Type filter + table "Typ" column (`contentLibrary.types.*` = Alle/Artikel/Häppchen exist; summary panel consumes them, table/filter don't). Note: de "Bite Size" should be "Häppchen".
- **CL#2 · P3 · [FE]** — Category filter trigger button shows "All" while its options localize to "Alle".
- **CL#3 · P3 · [FE]** — Bite-Size "N language(s)" count badge hardcoded English (no plural key).
- **CL#4 · P4 · [FE/BE TBD]** — Global "Ask Vantage Fit" assistant widget + suggestions English (may be intentional AI-only English). **(Cross-module — appears on all pages.)**
- **CL#5 · P4 · [FE]** — Action-column icon buttons have no accessible name.

### Programs → Create Content — `bug-logs/create-content.md`
- **CRC#1 · P2 · [FE]** — "Create content" type-picker modal entirely English; **not externalised** (no keys).
- **CRC#2 · P2 · [FE]** — Bite-Size Content Builder (`/fit/create-bite-size-content`) entirely English; **not externalised** (0 dict keys). *(Linked Content form, by contrast, is fully localized.)*

### Community → Events — `bug-logs/community-events.md`
- **EV#1 · P2 · [FE]** — Target-audience multiselect dropdowns render "All"/"All Countries"/"0 selected" English though `targetAudience.*` de keys exist. Same shared component as CC#3. *(Contrast: the attribute-style audience filter in Publish Notifications localizes — proof this is wire-up.)*
- **EV#2 · P3 · [FE]** — Event time picker uses 12-hour AM/PM instead of German 24-hour convention (locale format; product confirm).

### Community → Create Announcement — `bug-logs/create-announcement.md`
- **ANN#1 · P2 · [FE]** — Landing/list view 100% English despite a **complete** `announcementPage.*` (~66 keys) + `qna.announcement.*` German set. Pure wire-up.
- **ANN#2 · P2 · [FE]** — Create form mixed: AI-generate + Titel + Beschreibung localize, but heading, subtitle, "Audience & Delivery", city/country selectors and the **Publish** CTA stay English (keys exist).
- **ANN#3 · P3 · [FE]** — (dynamic flow) delete confirm-dialog ("Are you sure you want to delete?"/"…revert this!"/"Cancel"/"Delete") + success toast ("Success"/"Announcement successfully deleted.") render English though `announcementPage.deleteHeading/deleteText/success/deleteSuccess` German keys exist. Same wire-up as ANN#1/#2.

### Communications → Publish Notifications — `bug-logs/publish-notifications.md`
- **CLEAN** — 0 defects. Attribute audience filter localizes ("ist in"/"Alle Abteilungen").

### Communications → Send Custom Email — `bug-logs/send-custom-email.md`
- **SCE#1 · P3 · [FE/BE TBD]** — Email TEMPLATE preview mixes German injected placeholders with English boilerplate ("Open Vantage Fit", "Warm Regards, Vantage Fit Team", "Download the Vantage Fit app"). Email locale may intentionally follow the recipient / a template store — confirm FE vs BE/product. *(Admin page chrome is fully localized.)*

### Communications → Email Designer — `bug-logs/email-designer.md`
- **ED#1 · P2 · [FE]** — "Rich Email Composer" entirely English (stepper, value-prop, template gallery); **not externalised** (only sidebar/launcher keys exist).

### Workforce Health — `bug-logs/workforce-health.md`
- **Health Insights · [BE / external]** — ⛔ BLOCKED: embedded iframe `dash-vfit.vantagecircle.org` refused to connect; external analytics app, not localizable in-dashboard.
- **WS#1 · P2 · [FE]** — Wellness Score heavy mixed-language: stat cards, chart titles/subtitles, legends, correlation cards English despite a `wellnessScore.*` (49-key) namespace; frame/weights/counts/empty-states localize.
- **WL#1 · P3 · [FE]** — Wellness Leagues subtitle "Based on avg daily steps over 21 days" English (hardcoded/not-wired). *(Both pages also inherit RPT#1 filters + RPT#2 column selector.)*

### Rewards → Upload Points — `bug-logs/upload-points.md`
- Static page: CLEAN. **UP#1 · P3 · [FE]** (dynamic) — the CSV **"Preview" modal title is English** while its trigger button is "Vorschau" (German) — mixed-language. **UP#2 · P3 · [FE]** — upload **success toast "Success — File uploaded" renders English**. (Wallet/country/type selects, dropzone upload, preview, submit all functional; 1-pt test upload succeeded.)

### Configuration → Add Employees — `bug-logs/add-employees.md`
- **AE#1 · P3 · [FE]** — File-upload dropzone "Click to upload or drag and drop" English, while the identical control on Upload Points is German → wire-up inconsistency (string exists).
- **AE#2 · P3 · [FE]** (dynamic) — upload **success toast "The file was successfully uploaded. The processing time is typically 15 minutes…" renders English**. Also the shared CSV "Preview" modal title is English (UP#1). (Upload + preview + submit functional; test employee "QA Test Account" accepted.)

### Configuration → Preview Emails — `bug-logs/preview-emails.md`
- **PE#1 · P3 · [FE/BE TBD]** — All 9 email-type card titles + descriptions English (page chrome German); no `previewEmails.*` keys for them → hardcoded FE literals OR backend email-template metadata (triage).

### French & Spanish pass — `bug-logs/french-spanish-pass.md`
- **FR#1 · P3 · [FE]** — French label truncation: Settings banner/logo size chip clips "…recommand[ée]" (fixed-width overflow; Spanish/German/English fit). Recommend a French fixed-width-component truncation sweep.

---

## Classification index

### Frontend bugs [FE] — fixable in the web app now
Overview #1, #2, #3, #4, #5, #7 · CC#1, #2, #3, #5 · MGC#2 (UI, non-loc) · RPT#1, #2, #3, #4 · SET#2 ·
CL#1, #2, #3, #5 · CRC#1, #2 · EV#1, #2 · ANN#1, #2, #3 · ED#1 · WS#1 · WL#1 · AE#1, AE#2 · UP#1, UP#2 · DF#1 · FR#1.
  *(Dynamic-flow toasts: ANN#3 confirmed on delete + publish; UP#2/AE#2 = English upload success toasts; DF#1 = English generic loading toast — all frontend.)*
- Dominant sub-pattern: **wire-up gaps** — a translation exists in `fit/*.json` but the component renders
  English. Also: **not-externalised** newer builders (CRC#1/#2, ED#1), **locale formatting** (dates #5/RPT#4/CC#2,
  time EV#2, numbers), and **layout/truncation** (FR#1).

### Backend / data-driven [BE] — expected English until backend localization
- Health Insights (external embedded analytics app).
- Challenge **status** strings ("Active", "Not Started", "Ended on <date>") and **type** names
  ("Multi Week Multi Activity", "Journey/Race/E-Marathon Challenge") — from campaign/stream APIs.
- Deficiency names (Vitamin D, Sleep Quality, Stress Levels), health-status values (Normal, Needs Attention),
  tier names (Gold/Silver/Bronze), plan name ("Grow"), challenge/event/content **names**, report **cell data**
  (names, emails, department, country, product), Reports "Grund"/reason descriptive text — all data/copy.
- (These are documented per-module as "Backend/data — NOT frontend bugs"; listed here for completeness.)

### To be identified — Frontend or Backend [FE/BE TBD] — needs triage / product confirmation
- **Overview #6** — number/percentage/currency formatting (FE formatter vs API values; currency intent).
- **CC#4** — ~24 activity/task-type names (likely backend activities master list).
- **MGC#1** — "Ends In X Days" card countdown (backend statusString vs FE literal).
- **RPT#5** — currency code+integer ("INR 25") vs locale currency (product intent).
- **PE#1** — 9 email-type card titles/descriptions (hardcoded FE vs backend email-template metadata).
- **SCE#1** — email template boilerplate language (may follow recipient locale by design).
- **CL#4** — "Ask Vantage Fit" assistant widget copy (may be intentional English-only for now).
- **SET#1** — language-switcher option names in English (endonyms vs English is a product/design call).

---

## Cross-cutting patterns (for dev triage)
1. **Wire-up against a complete dictionary** — de/fr/es `fit/*.json` are 991/991 keys complete, yet many
   components render English literals or wrong keys. Fixing the wiring resolves the bulk (Overview #1/#2,
   CC#1/#3, RPT#1/#2/#3, CL#1/#2, EV#1, ANN#1/#2, WS#1, AE#1).
2. **Newer rich-builders shipped without i18n** — Bite-Size Content Builder (CRC#2) and Email Designer (ED#1)
   have zero dictionary keys; the Create-Content picker (CRC#1) too.
3. **Locale-unaware formatters** — one shared bug family for dates (Overview #5, RPT#4, CC#2/#5 calendars) and
   time (EV#2); standardise on a locale-aware date/number adapter.
4. **Shared components fixed once, fixed everywhere** — report filter bar (RPT#1 → Wellness Score/Leagues),
   column selector (RPT#2 → Wellness Leagues), target-audience multiselect (EV#1 = CC#3), the date-picker
   calendar (CC#2 = RPT#4), `<html lang>` (Overview #4, every page), Ask-VF widget (CL#4, every page).
5. **Generic/loading toasts unwired — DF#1 · P3 · [FE]** — the shared request toast "This request is taking
   longer than expected. Please wait..." renders English (seen during Create-Content image processing);
   likely a global HTTP-interceptor message → appears app-wide on slow requests. Contrast: most success
   toasts (notification/email send, content create, settings save) ARE localized.

## Not yet covered (engagement gaps)
- Dynamic flows (submit/send/publish/upload) + their validation & success toasts — deferred everywhere to
  avoid tenant writes (best on a throwaway/E2E tenant).
- Exhaustive per-module French/Spanish click-through + a full fixed-width truncation sweep in French.
- US / Europe / E2E servers (India-only so far).
