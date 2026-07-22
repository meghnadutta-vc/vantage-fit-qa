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
