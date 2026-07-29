# Dynamic-Flow Localization — Validation & Toasts (cross-module)

**Scope:** submit/send/publish/delete flows + their validation messages and success/error toasts.
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`, company 355, UAT) · **Language:** German (deep)
**Executed:** 2026-07-22 · Evidence: `evidence/dynflow_*`

> These are the flows deferred during the static pass (they require real submissions). User authorized
> live submits incl. outward sends. Minimization applied: test-labelled data ("QA localization test"),
> outward sends targeted the **admin account only** (Anjan Pathak) where an audience could be picked,
> and cleanup where possible. Toasts are transient → captured via an in-page MutationObserver.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Steps | Expected | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|
| DYN-TC-001 | Required-field validation pattern | Create Challenge / Create Event: attempt submit with empty required fields | Localized error OR safe block | **Preventive** — submit button is `aria-disabled` until the form is valid (no inline error strings). Text fields use hard `maxlength` (blocks input, no error). Helper text localized ("Maximal 2000 Zeichen erlaubt"). No validation-error strings to localize. | PASS | P3 |
| DYN-TC-002 | Publish Notifications — send success toast | Fill title, add self as sole recipient, send | Localized toast | **Localized ✓** — "✓ Benachrichtigung an 1 Benutzer gesendet." (also counts correctly). Sent only to admin. Evidence: dynflow_publishnotif_de_toast.png | PASS | P2 |
| DYN-TC-003 | Send Custom Email — send success toast | Fill subject+heading, add self as sole recipient, send | Localized toast | **Localized ✓** — "✓ E-Mail an 1 Benutzer gesendet." Sent only to admin. | PASS | P2 |
| DYN-TC-004 | Announcement — delete confirm dialog | Delete an existing (junk) announcement | Localized dialog | **English ✗** — "Are you sure you want to delete?" / "You won't be able to revert this!" / "Cancel" / "Delete" though `announcementPage.deleteHeading`="Möchten Sie wirklich löschen?" / `.deleteText` / `common.cancel` exist. See **ANN#3**. Evidence: dynflow_announcement_de_deletedialog.png | FAIL | P3 |
| DYN-TC-005 | Announcement — delete success toast | Confirm delete | Localized toast | **English ✗** — "Success" / "Announcement successfully deleted." though `announcementPage.success`="Erfolg" / `.deleteSuccess`="Ankündigung erfolgreich gelöscht." exist. See **ANN#3**. | FAIL | P3 |
| DYN-TC-006 | Announcement — publish success toast | Publish an announcement | Localized toast | NOT EXECUTED — publishing posts to the org feed for a whole city/country (audience can't be narrowed to self). Given the delete flow proves the module's dynamic strings are unwired (ANN#3) + static ANN#1/#2, the publish toast is expected English; not verified to avoid an org-wide post. | NEEDS VERIFICATION | P3 |
| DYN-TC-007 | Preview Emails — enable/disable save toast | Toggle an email + save | Localized toast | NOT EXECUTED — toggling governs real org-wide system-email sending; `previewEmails.saved`="E-Mail-Einstellungen erfolgreich gespeichert." exists (German). Needs verification. | NEEDS VERIFICATION | P3 |
| DYN-TC-008 | Settings — save feedback | Change a setting → Save | Localized | Save-bar clears on success; no text toast (verified in the Settings module pass). PASS (no toast to localize). | PASS | P3 |
| DYN-TC-009 | Create Event / Create Content — create success toast | Complete + submit | Localized toast | NOT EXECUTED — both require a mandatory image upload + create persistent tenant content/events (no clean UI delete). Deferred; recommend running with disposable data. | NEEDS VERIFICATION | P3 |
| DYN-TC-010 | Upload Points / Add Employees — upload validation + success toast | Upload CSV (bad → error; good → success) | Localized toasts | NOT EXECUTED — success path distributes real points / adds real employees (persistent). Error path (malformed CSV) safe but not run this pass. Needs verification. | NEEDS VERIFICATION | P3 |
| DYN-TC-011 | French/Spanish dynamic strings | Repeat toasts in fr/es | Localized | NOT EXECUTED — toast strings are language-agnostic in behaviour; localized ones (Comms) have fr/es dict values, unwired ones (Announcements) will be English in all langs. Needs verification. | NEEDS VERIFICATION | P3 |

---

## Run 2 — 2026-07-22 (German; more modules + functional clicks/redirects)

> Re-login required (sessions expired → Microsoft SSO dead-end); resumed after manual login as
> meghna.dutta@vantagecircle.com. Outward sends targeted the admin only where possible.

| Test Case ID | Description | Steps | Expected | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|
| DYN-TC-020 | **Sidebar navigation / redirection sweep** | Click all 24 sidebar links | Each routes to its page | **24/24 PASS** — every leaf link (Overview, Challenges×3, Programs×2, Community×3, Comms×2, Workforce×3, Reports×6, Upload Points, Config×3) navigates to the correct route; all groups expand. | PASS | P2 |
| DYN-TC-021 | Create Content (Linked) — create success toast | Fill form + image upload + crop + submit | Localized toast + redirect | **Localized ✓** — "Inhalt erfolgreich erstellt". Image crop dialog localized ("Absenden"/"Abbrechen"). Form + upload + crop + submit all functional. (Created test item "QA localization test — please delete" → cleanup.) | PASS | P2 |
| DYN-TC-022 | **Create Content — request/loading toast** | During image processing | Localized | **English ✗** — "This request is taking longer than expected. Please wait..." (generic request/loading toast). See **DF#1**. | FAIL | P3 |
| DYN-TC-023 | Preview Emails — save toast | Toggle an email + "Änderungen speichern" | Localized toast | **Localized ✓** — "E-Mail-Einstellungen erfolgreich gespeichert."; save button "Änderungen speichern" localized. Toggle + save + restore functional (config restored to 9/9). | PASS | P2 |
| DYN-TC-024 | Upload Points — functional (wallet/country/type/upload/preview) | Select Reward + India + Primär, upload CSV, Preview | Works + localized | Functional ✓ (selects, dropzone upload via hidden file input, Preview all work). **Preview modal TITLE "Preview" is English** (button "Vorschau" localizes) — mixed. See **UP#1**. Not submitted (no points distributed). | FAIL | P3 |

| DYN-TC-025 | Add Employees — upload + preview | Upload CSV → preview auto-opens | Works + localized | Functional ✓ (hidden file input → preview auto-opens). **Preview modal title "Preview" English** — same shared component as Upload Points → **UP#1** applies here too. Not submitted (no employees added). Evidence: dynflow_addemployees_de.png | FAIL | P3 |
| DYN-TC-026 | Manage Challenges — "Verwalten" redirect | Click Verwalten on a card | Navigates to edit page | ✓ → `/fit/manage-challenge/edit-challenge/25423`. Click + redirect work. | PASS | P2 |
| DYN-TC-027 | Employee Report — load + Export | Open report; click Exportieren | Data loads; export works | ✓ report auto-loads data; "Exportieren" opens a CSV/Excel format menu (functional). Filters/column-selector show RPT#1/#2 English (known). Evidence: dynflow_employeereport_export_de.png | PASS | P2 |
| DYN-TC-028 | Content Library — row Edit + delete availability | Open a content row's action | Edit opens; delete available | Edit modal opens pre-filled ✓ — but **no delete action exists** in the Content Library UI (edit only). Also the EDIT modal reuses the "Verknüpften Inhalt erstellen" (create) title. See notes. | ◐ | P4 |

## Phase 4 — Summary

- **Validation:** preventive across the board — the shared design-system submit button stays `aria-disabled`
  until valid, and text inputs enforce `maxlength`. **No inline validation-error strings exist**, so nothing
  to mistranslate there.
- **Toasts are inconsistently wired** — the exact same pattern as the static findings:
  - **Localized ✓:** Publish Notifications, Send Custom Email (Communications) — success toasts render German
    and even count correctly ("an 1 Benutzer").
  - **English ✗:** Announcements delete dialog + success toast (**ANN#3**) — despite complete German keys.
- **Net new bug: ANN#3 (P3)** — Announcement delete confirm-dialog + success-toast render English (wire-up gap,
  same root as ANN#1/#2). Everything else confirmed either localized or preventive.
- **Deferred (need disposable data / would create persistent records or post org-wide):** Announcement publish
  toast, Preview-Emails save toast, Create Event/Content create toasts, Upload-Points/Add-Employees upload
  toasts, and the fr/es repeat. Recommend a follow-up on a throwaway/E2E tenant.
- **Test data:** sent 1 notification + 1 email to the admin account (Anjan Pathak) only; deleted a few junk
  "Test Announcement" rows (cleanup). No new persistent content created.

## Run 3 — 2026-07-22 (remaining success-path submits, UAT creation authorised, formal names)

| Test Case ID | Description | Actual Result | Status | Priority |
|---|---|---|---|---|
| DYN-TC-030 | Content Library — edit/update item | Edit modal update saved; toast "Inhalt erfolgreich erstellt" (localized ✓) — but reuses the CREATE wording ("erstellt") for an update. Item renamed to formal "Managing Workplace Stress: A Practical Guide". | ◐ | P4 |
| DYN-TC-031 | Upload Points — real upload success toast | Uploaded valid CSV (1 pt to self). Toast **"Success — File uploaded" ENGLISH** → **UP#2**. | FAIL | P3 |
| DYN-TC-032 | Announcement — publish success toast | Published "Q3 Wellness Program — Now Live" (Atlanta/US). Toast **"Success — Announcement creation in progress ..." ENGLISH** → confirms **ANN#3** on the publish path (delete + publish both English). | FAIL | P3 |
| DYN-TC-033 | Add Employees — upload success toast | Uploaded valid CSV ("QA Test Account", status=1). Toast **"The file was successfully uploaded. The processing time is typically 15 minutes, although this can vary." ENGLISH** → **AE#2**. Also DF#1 loading toast reproduced. | FAIL | P3 |
| DYN-TC-034 | Create Event — full submit | NOT COMPLETED — date fields are calendar-only (ignore programmatic/typed values); form needs ~15 custom-widget interactions. Components (date/time/audience/image) already functionally verified; create-toast expected localized (per Create Content). Deferred. | NEEDS VERIFICATION | P3 |

**Toast-localization pattern (refined):** LOCALIZED — Publish Notifications send, Send Custom Email send,
Create Content create ("Inhalt erfolgreich erstellt"), Preview-Emails save. ENGLISH — Announcement delete
dialog+toast+publish (ANN#3), Upload-Points success (UP#2), Add-Employees success (AE#2), generic loading
toast (DF#1). Newer upload/interceptor/announcement toasts are unwired; core send/create/save toasts localize.

**Test data created (UAT company 355, formal names):** Content item "Managing Workplace Stress: A Practical
Guide" (no UI delete); 1 point to self; announcement "Q3 Wellness Program — Now Live" (Atlanta/US, deletable
via list); employee "QA Test Account" / qa.test.account@vantagecircle.com (deactivate via status=0 if needed).

## Run 2 notes (2026-07-22)
- **Functional:** navigation/redirection 24/24 PASS; Manage-Challenges "Verwalten" → edit page ✓; Employee
  Report loads + "Exportieren" → CSV/Excel menu ✓; Content-Library row action opens an Edit modal ✓ (but
  NO delete action exists in the Content Library UI — edit only; and the Edit modal reuses the *create*
  title "Verknüpften Inhalt erstellen").
- **Toasts:** Create Content create ("Inhalt erfolgreich erstellt") + Preview-Emails save ("E-Mail-
  Einstellungen erfolgreich gespeichert.") LOCALIZED. New: **DF#1** generic loading toast English; **UP#1**
  Upload-Points + Add-Employees shared CSV "Preview" modal title English.
- **⚠ Cleanup pending:** created a test Linked-Content item **"QA localization test — please delete"** —
  it CANNOT be removed via the UI (no delete control in Content Library); needs backend/manual removal.
- **Still deferred:** Create Event full-submit create toast (mega-form; components already functionally
  verified; toast expected localized), Add-Employees/Upload-Points success toasts (would add people /
  distribute points), Announcement publish toast (org-wide; ANN#3 shows its strings are English).
