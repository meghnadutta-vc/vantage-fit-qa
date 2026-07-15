# End-to-End Website Testing Checklist

**Module:** Log Smoking (Track Habits tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | 🔵 Partially — modal opens, radio selection enables Save, Save genuinely persists to the backend (200, "Activity Saved Successfully" confirmed via network inspection); **but** the saved value is never surfaced anywhere in the web UI afterward (#43); N/A: search/filter/pagination/upload for this module |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | ✅ Done (Save correctly stays disabled until a radio is selected; toggling between Yes/No works correctly; N/A: no free-text fields to fuzz for XSS) |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ✅ Done (Close button, +Add entry point tested; date nav Previous/Next day present, Next day correctly disabled on Today) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ❌ **Failed** — on mobile, the floating "Chat with us" widget overlaps the left portion of the Save button (#45); touch targets undersized (#44); no toast/success feedback observed on save |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | 🔵 Partially — mobile 390×844 tested via bottom-nav FAB; modal renders as a clean bottom sheet but the "Chat with us" widget overlap (#45) is mobile-specific; tablet/zoom/orientation-change not separately tested |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done (all modal copy reviewed — no issues found; supportive helper text is a nice touch) |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | ❌ **Failed** — modal has no focus trap at all; focus stays on `<body>` on open and Tab moves to the background header's overflow-menu button (reproduces Bug #33 pattern); full contrast-ratio measurement and screen-reader pass not done |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | ❌ **Failed** — the backend genuinely saves the record (confirmed via network inspection: `POST /vantagefit/api/v1/activity/save` → 200, "Activity Saved Successfully", real `userActivityId` returned) but this is **never displayed anywhere** in the web UI: no Diary → Vitals row, no Summary → Trends tile, and reopening the modal does not pre-fill the just-saved answer, even within the same session (#43) |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | ⬜ Not done (no toast observed on save — not specifically probed) |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Scope note (Avoid Sugar)
- Verified live on 2026-07-14: the +Add → Track Habits tab contains **only** "Log Smoking". There is
  no "Avoid Sugar" submodule anywhere in the Quick Add dropdown (checked all 4 tabs: Workout,
  Mindfulness, Log Diary, Track Habits). SCOPE.md has been updated to remove the assumption that
  it exists under +Add; if "Avoid Sugar" exists elsewhere in the product (e.g. a separate Habits
  page), it was not discovered from the Summary/Diary entry points tested and needs a human
  pointer to the correct location.

## Data reflection (explicitly requested this pass)
- **Backend save confirmed real**: network inspection of the Save action shows
  `POST /vantagefit/api/v1/activity/save` with body
  `{"activity_id":1013,"activity_name":"Log Smoking","activity_type":"adherence","measuring_unit":"count","value":1,...}`
  returning `200 {"status_message":"Activity Saved Successfully","userActivityId":2295087}` —
  the log is genuinely being written server-side.
- **However**, nothing on the web dashboard ever shows this value: Diary → Vitals only lists
  Mood/Heart Rate/Weight (no Smoking row); Summary → Trends has no habit tile; and reopening the
  Log Smoking modal always resets to no selection, even moments after a successful save in the
  same session. Confirmed with a hard reload to rule out stale cache. This is a genuine, verified
  gap — not a cache artifact — filed as Bug #43.

## Bugs found this pass (Log Smoking)
- Bug #42 — Modal has no focus trap; focus stays on `<body>` on open, Tab moves to the background header's overflow-menu button (P2, Accessibility)
- Bug #43 — Log Smoking saves succeed on the backend (confirmed via network inspection) but are never reflected anywhere in the web UI: no Diary Vitals row, no Summary trend tile, no reopen pre-fill (P2, Functional/Data)
- Bug #44 — Touch targets under 44×44px: Close (32×32), Previous/Next day (29×29), Save (375×43, 1px under) (P3, Accessibility)
- Bug #45 — Mobile "Chat with us" floating widget overlaps the left portion of the Save button in the Log Smoking bottom sheet (P3, UI/UX)

## Notes/Doubts this pass (Log Smoking)
- **Avoid Sugar does not exist under +Add**: confirmed by opening the live Track Habits tab —
  it contains only "Log Smoking". This was flagged to the user before testing and confirmed as
  out of scope for this session rather than assumed.
- The radio-based Yes/No design and disabled-until-selected Save button are a clean, simple
  pattern — no complaints on the interaction model itself, only on what happens after Save.
- Given the backend save is real (confirmed `userActivityId`), this looks like a **missing
  frontend read/render path** rather than a broken write path — worth flagging to dev as
  potentially a quick fix (the data already exists, it just isn't being queried/displayed).

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/log-smoking.md`.
