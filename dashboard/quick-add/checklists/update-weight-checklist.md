# End-to-End Website Testing Checklist

**Module:** Update Weight (Log Diary tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | ✅ Done (unit toggle, slider, +/− steppers, Save/Update weight submit, editing an existing entry all tested; N/A: search/filter/pagination/upload for this module) |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | ✅ Done (lbs↔kg conversion round-trips correctly; N/A: no free-text fields to fuzz for XSS in this module) |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ✅ Done (Close button, +Add entry point vs. Diary's "Edit weight"/"Log weight" button both tested) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ✅ Done (layout reviewed; touch targets measured; **found:** wrong default value before first log today (#40); **positive:** contextual "Save"/"Update weight" button copy, "Log weight"/"Edit weight" Diary label switch) |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | ✅ Done (mobile 390×844 tested via bottom-nav FAB — clean bottom-sheet layout, no floating-widget overlap unlike Track Mood/Log Sleep; tablet/zoom/orientation-change not separately tested) |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done (all modal copy reviewed — no grammar issues found) |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | 🔵 Partially — the weight-scale slider IS keyboard-operable (ArrowRight/Left correctly change the value, synced with the display) — a genuine positive contrast with Log Sleep's Bug #26; full focus-trap-on-open, contrast-ratio measurement, and screen-reader pass not done |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | ✅ Done (mostly) — saved weight (164.4 lbs) correctly persisted and reflected in both Summary → Vitals and Diary → Vitals after a hard reload; **however**, the modal's own **default value before any log exists is wrong** (#40) — a real data-integrity gap in what the modal itself displays, separate from whether a save persists correctly |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | ⬜ Not done (no toast observed on save — not specifically probed) |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Data reflection (explicitly requested this pass)
- **Summary → Vitals:** Updated from "132.28 lbs / Updated on 29 Jun 2026" → "164.4 lbs / Updated on
  14 Jul 2026" after saving and a hard reload. Confirmed correct (same known stale-cache-until-reload
  behavior as other modules, e.g. Bug #8).
- **Diary → Vitals:** Updated to "164.4 lbs" and the button label correctly switched from "Log weight"
  to "Edit weight", mirroring Mood's existing "Edit mood" pattern. Confirmed correct.
- **However**, before any weight was logged today, the modal's own default "latest weigh-in" value
  (165.0 lbs) did **not** match the real last-known weight shown on Summary → Vitals (132.28 lbs) — this
  is a bug in the modal's default-value logic, not in how a save subsequently persists (Bug #40).

## Bugs found this pass (Update Weight)
- Bug #40 — Wrong default "latest weigh-in" value shown before today's first log (165.0 lbs/74.8 kg) vs. the actual last-known weight (132.28 lbs) — reproduced via both desktop +Add and mobile FAB, even after logging today's weight when reopened via +Add rather than Diary's edit flow (P2, Functional/Data)
- Bug #41 — Touch targets under 44×44px: Close (32×32), Prev/Next day (29×29), lbs/kg toggle (29×29) — Reduce/Increase weight (48×48) and the submit button (375×48) both pass (P3, Accessibility)

## Notes/Doubts this pass (Update Weight)
- The weight-scale slider (`role="slider"`, `tabindex="0"`) is genuinely keyboard-operable — ArrowRight/
  ArrowLeft move the value in 0.2 lb increments, correctly synced with the numeric readout and the +/−
  steppers. This is a clear positive contrast with Log Sleep's Bug #26 (non-operable sliders).
- Contextual submit-button copy is a nice detail: "Save" for the first log of the day, "Update weight"
  when editing an existing entry.
- Editing an existing entry via Diary's "Edit weight" button correctly pre-fills the just-saved value
  and labels it "Same as last log" — this is the correct behavior; the bug (#40) is specific to the
  *before any log exists today* default, and to reopening via +Add rather than the Diary edit flow.
- Reduce/Increase weight (+/−) steppers pass the touch-target minimum at 48×48px, unlike most other
  controls measured across this project so far.

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/update-weight.md`.
