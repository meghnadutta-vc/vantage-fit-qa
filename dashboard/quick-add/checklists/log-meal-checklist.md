# End-to-End Website Testing Checklist

**Module:** Log Meal (Log Diary tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | ✅ Done — confirmed this is a mobile hand-off (labeled "Track on app"), correctly opens the same "Continue this in the Vantage Fit app" QR modal used by Sync Steps History/Measure Heart Rate; "Save QR" button present. N/A: no web-native CRUD flow exists for this module by design |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | N/A — this module has no web-based input fields; it is a QR hand-off only |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ❌ **Failed** — Close (×) button closes the modal but leaves the Quick Add dropdown open (reproduces Bugs #1/#2) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ❌ **Failed** — dropdown remains visibly open behind/around the QR modal (reproduces Bug #1) |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | ⬜ Not separately tested this pass (desktop only; same QR-modal pattern already covered for other Track-on-app modules) |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done — "MOBILE APP" tag, "Continue this in the Vantage Fit app", subtitle all reviewed, no issues found, identical pattern to other Track-on-app modals |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | ❌ **Failed** — keyboard focus never moves into the QR modal on open; reproduces Bug #3 |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | N/A — no data is ever logged on the web side for this module (pure hand-off) |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | N/A — no web-side save action to give feedback on |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Bugs found this pass (Log Meal)
- No new numbered bugs — this module **reproduces the existing Bugs #1, #2, and #3** exactly (dropdown
  left open behind the modal, Close button not closing the dropdown, no focus trap on the QR modal).
  See Notes below and the bug-log's "Log Diary" section for the confirmed-reproduction writeup.

## Notes/Doubts this pass (Log Meal)
- **Reproduces Bug #1**: the Quick Add dropdown (Log Diary tab, Log Water/Update Weight/Log Meal rows)
  remains fully visible behind/around the "Continue this in the Vantage Fit app" QR modal.
- **Reproduces Bug #2**: closing the QR modal via its Close (×) button leaves the dropdown open;
  confirmed via a full-page screenshot immediately after clicking Close.
- **Reproduces Bug #3**: `document.activeElement` stays on the "Log Meal" trigger button when the modal
  opens; pressing Tab moves focus to the header's overflow-menu button next, never into the modal.
- This is now confirmed across **three** Track-on-app modules (Sync Steps History, Measure Heart Rate,
  Log Meal) — strong evidence this is a systemic issue in the shared "Continue in app" QR modal
  component itself, not specific to any one trigger.

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/log-meal.md`.
