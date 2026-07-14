# End-to-End Website Testing Checklist

**Module:** Log Water (Log Diary tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | ✅ Done (Add/Remove a glass, unit toggle round-trip, over-goal handling, Clear, Save all tested; N/A: search/filter/pagination/upload for this module) |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | ✅ Done (tested well past the 8-glass/2000 ml goal — handled gracefully with no cap and a "Daily goal reached" message; N/A: no free-text fields to fuzz for XSS in this module) |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ✅ Done (Close button, +Add entry point vs. Diary's "Log water" button both tested) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ✅ Done (layout reviewed; touch targets measured; **found:** unit label doesn't convert on fl oz toggle (#37), reopening doesn't reflect today's total (#36); Remove-a-glass disabled state at 0 confirmed) |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | ✅ Done (mobile 390×844 tested via bottom-nav FAB — clean bottom-sheet layout, no floating-widget overlap unlike Track Mood/Log Sleep; tablet/zoom/orientation-change not separately tested) |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done (all modal copy reviewed — no grammar issues found aside from the stale unit label, #37) |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | ❌ **Failed** — modal has no focus trap at all (#33); the "Any amount" ruler has no role/tabindex and is completely unreachable by keyboard (noted, not separately filed since the Glasses stepper covers the same function); full contrast-ratio measurement and screen-reader pass not done |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | ❌ **Failed** — Diary Intake → Water shows a wrong unit/value ("25.36/ 2.5 L" instead of "0.75/ 2 L"), confirmed persistent after hard reload (#34); no Water trend tile exists in Summary at all (#35); reopening the modal doesn't reflect today's already-logged total (#36) |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | ⬜ Not done (no toast observed on save — not specifically probed) |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Data reflection (explicitly requested this pass)
- **Diary → Intake → Water:** Updated after logging 750 ml, but with the **wrong unit and wrong goal**
  ("25.36/ 2.5 L" instead of the expected "0.75/ 2 L") — confirmed after a hard reload, not a stale-cache
  artifact (Bug #34).
- **Summary → Trends:** No Water/Hydration tile exists at all — a parity gap versus Sleep/Steps, though
  less severe than Guided Meditation's Bug #30 since the value IS visible on Diary, just wrong (Bug #35).
- Reopening Log Water (via +Add or Diary) after logging does **not** reflect the running total already
  saved — always resets to a blank 0 ml/0 glasses state (Bug #36), the same root cause as Bugs #23/#27.

## Bugs found this pass (Log Water)
- Bug #33 — Modal has no focus trap; background page remains fully tabbable while open (P2, Accessibility)
- Bug #34 — Diary Intake → Water shows wrong unit/value ("25.36/ 2.5 L") vs. the correct "0.75/ 2 L" (P2, Functional/Data)
- Bug #35 — No Water/Hydration trend tile exists in Summary → Trends at all (P3, Functional/Data)
- Bug #36 — Reopening the modal doesn't reflect today's already-logged total (P3, Functional)
- Bug #37 — "Glasses" sub-label ("1 glass = 250 ml") doesn't convert when unit is toggled to fl oz (P4, Copy/UI)
- Bug #38 — Touch targets under 44×44px: Close (32×32), Prev/Next day (29×29), Remove/Add glass (34×34), unit toggle (51×31) (P3, Accessibility)
- Bug #39 — Mobile "+" FAB (opens Quick Add) has the wrong accessible name, "Give recognition" (P2, Accessibility/Functional)

## Notes/Doubts this pass (Log Water)
- The "Any amount" drag-to-fine-tune ruler is entirely keyboard-inaccessible (no role/tabindex, caret
  is aria-hidden) — not filed as a separate numbered bug since the Glasses +/− stepper achieves the same
  outcome and IS keyboard-operable, but worth a dedicated accessibility pass later.
- Over-goal behavior (tested to 12 glasses/3000 ml, well past the 8-glass/2000 ml goal) is a genuine
  positive finding — no artificial cap, graceful "Daily goal reached" messaging.
- Clear button works correctly, resetting both glass count and ruler value to 0.
- Mobile Quick Add is accessed via a bottom-nav "+" FAB (there is no header "Quick add" button on
  mobile) — functionally correct but see Bug #39 for its accessible-name issue.

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/log-water.md`.
