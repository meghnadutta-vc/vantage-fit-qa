# Configuration → Preview Emails Module — Localization Test Cases

**Module:** Vantage Fit → Configuration → Preview Emails (`/fit/configuration/preview-emails`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT · **Language:** German (deep) vs English
**Executed:** 2026-07-21 · Evidence: `evidence/previewemails_de.png` · Read-only (no enable/disable saved).

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| PE-TC-001 | Header + subtitle + count localized | de | Read header | Localized | "E-Mail-Vorschau" / "Verwalten Sie die an Mitarbeitende gesendeten E-Mail-Benachrichtigungen. 9 von 9 aktiviert" (consumes previewEmails.title/subtitle/enabledCount). PASS. | PASS | P2 |
| PE-TC-002 | Toggle labels localized | de | Read toggles | Localized | "Deaktivieren"; locked "Diese E-Mail kann nicht geändert werden" (previewEmails.cannotModify). PASS. | PASS | P2 |
| PE-TC-003 | "Open in new tab" links localized | de | Read links | Localized | "In neuem Tab öffnen" (previewEmails.openNewTab). PASS. | PASS | P3 |
| PE-TC-004 | Footer "about" note localized | de | Read footer | Localized | "Über die E-Mail-Einstellungen" + full German body (previewEmails.aboutTitle/aboutText). PASS. | PASS | P3 |
| PE-TC-005 | **Email-type card titles + descriptions localized** | de | Read 9 cards | Localized | All 9 English: "Welcome Email (Add Employee)"/"Received when an employee is added…", "Welcome Email (Invite to Challenge)", "Intro to App", "Challenge Reminder", "Challenge Start", "Weekly Summary", "Challenge Completion", "Event Invite / RSVP Confirmation", "Direct Message from HR" (+ descriptions). See **PE#1**. Evidence: previewemails_de.png | FAIL | P3 |
| PE-TC-006 | Enable/disable save toast + discard dialog localized | de | Toggle + navigate | Localized | NOT EXECUTED — avoided changing tenant email config; keys exist (previewEmails.saved/saveFailed/discardTitle/discardText — all German). Needs verification (likely PASS). | NEEDS VERIFICATION | P3 |
| PE-TC-007 | French / Spanish | fr/es | Repeat | Localized | NOT EXECUTED. Needs verification. | NEEDS VERIFICATION | P3 |

## Phase 4 — Summary
Page chrome fully localized (whole `previewEmails.*` namespace consumed, incl. save/discard toast+dialog
keys). **PE#1 (P3):** the 9 email-type card titles + descriptions render English — **no matching frontend
i18n keys exist** (the `previewEmails.*` namespace covers only chrome), so these are either hardcoded
literals or backend-provided email-template metadata; needs FE/BE confirmation. Not executed: save toast /
discard dialog (avoided changing tenant email settings), fr/es.
