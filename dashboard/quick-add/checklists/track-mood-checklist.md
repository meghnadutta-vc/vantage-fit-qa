# End-to-End Website Testing Checklist

**Module:** Track Mood (Mindfulness tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | ✅ Done (mood select, save, edit/update, day nav — all core flows tested; N/A: search/filter/pagination/upload/3rd-party for this module) |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | ✅ Done (Save-disabled-until-selected validated; duplicate-submit-safety validated via re-save/update; N/A: no free-text fields to fuzz for boundary/XSS in this module) |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ✅ Done (Previous/Next day navigation, Close button, +Add entry point vs. Diary Edit-mood entry point all tested) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ✅ Done (layout/spacing reviewed across all 5 mood states; color-semantics bug found; touch-target sizes measured; z-index overlap found on mobile) |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | ✅ Done (mobile 390×844 tested — found floating-widget overlap bug; touch targets measured on both viewports; tablet/zoom/orientation-change not separately tested) |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done (all mood-specific follow-up copy reviewed — no grammar issues found) |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | ⬜ Not done (touch-target sizing covered under UI/UX above; full keyboard-nav and contrast-ratio measurement not done for this module) |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | ⬜ Partially — persistence after save/edit confirmed; refresh/logout-login and concurrent-edit scenarios not tested |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | ⬜ Not done (no toast observed on save — not specifically probed) |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Bugs found this pass (Track Mood)
- Bug #22 — Save button color inverted relative to mood sentiment (P2)
- Bug #23 — +Add entry point doesn't recognize/pre-fill today's already-logged mood (P3)
- Bug #24 — Mobile: floating Chat widget + FAB overlap the Save button (P2)
- Bug #25 — Touch targets under 44×44px for Prev/Next day arrows and Close button (P3)

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/track-mood.md`.
