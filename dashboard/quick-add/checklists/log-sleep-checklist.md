# End-to-End Website Testing Checklist

**Module:** Log Sleep (Mindfulness tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | ✅ Done (time-asleep steppers, bedtime/wake-up sliders, day nav, save/persist — all core flows tested; N/A: search/filter/pagination/upload/3rd-party for this module) |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | ✅ Done (stepper floor at 0h/ceiling at "in bed" duration validated; "in bed" duration recalculation on slider change validated; N/A: no free-text fields to fuzz for XSS in this module) |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ✅ Done (Previous/Next day navigation, Close button, +Add entry point vs. Diary tested; Next-day-disabled-on-Today confirmed) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ✅ Done (layout/spacing reviewed on desktop — clean; touch-target sizes measured for arrows/close/steppers/slider handles; z-index overlap found on mobile; keyboard-inaccessibility found on sliders) |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | ✅ Done (mobile 390×844 tested — found floating-widget overlap bug, same as Track Mood; touch targets measured on desktop; tablet/zoom/orientation-change not separately tested) |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done ("Time asleep", "in bed", Bedtime/Wake up labels reviewed — no grammar issues found) |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | ⬜ Partially — slider keyboard-operability tested and found broken (Bug #26); full focus-order/contrast-ratio/screen-reader pass not done |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | ✅ Done — persistence confirmed in both Diary ("8 hrs 0 mins / Total sleep duration") and Summary (Trends → Avg Sleep "8 hrs 0 mins ↑") after hard reload; concurrent-edit scenario not tested |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | ⬜ Not done (no toast observed on save — not specifically probed) |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Summary-page data reflection (explicitly requested this pass)
- **Diary:** Sleep card updated from "No Data" / "Track your sleep to see insights" → "8 hrs 0 mins / Total sleep duration". Confirmed correct.
- **Summary:** Trends → "Avg Sleep" tile updated from "0 sec ↓" → "8 hrs 0 mins ↑". Required a hard reload to display (same pre-existing stale-cache behavior as Bug #8 for Log Activity) — confirmed correct after reload.

## Bugs found this pass (Log Sleep)
- Bug #26 — Bedtime/Wake up sliders not keyboard-operable (arrow keys have no effect; mouse drag works) (P2, Accessibility)
- Bug #27 — +Add entry point doesn't pre-fill today's already-logged sleep data (same pattern as Bug #23, now confirmed systemic across Mindfulness module) (P3)
- Bug #28 — Mobile: floating Chat widget + FAB overlap the Save button (same as Bug #24, confirmed global) (P2)
- Bug #29 — Touch targets under 44×44px for Prev/Next day arrows and Close button; slider handles borderline at 42×42px (P3)

## Notes/Doubts this pass (Log Sleep)
- No edit affordance for a logged Sleep entry in Diary, unlike Mood's "Edit mood" — flagged as a product question, not a confirmed bug.
- "In bed" duration correctly recalculates on slider drag while preserving the prior "time asleep" value when it still fits the new window.

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/log-sleep.md`.
