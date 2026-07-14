# End-to-End Website Testing Checklist

**Module:** Guided Meditation (Mindfulness tab, +Add / Quick Add)
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Date:** 2026-07-14

Checked items were completed this pass. Unchecked items are pending for a future session.

| Category | What to test | Status |
|---|---|---|
| **Functional** | Every button/link works; forms submit correctly; CRUD (create/read/update/delete); search & filters; pagination; sorting; file upload/download; third-party integrations; redirects; deep links | ✅ Done (navigation to library page, featured "Start session", category session cards, detail dialog → "Begin session" → player, Pause/Play, Rewind/Forward 10s, deep-link support all tested; N/A: search/filter/pagination/upload for this module — it's a static curated library, not a searchable list) |
| **Validation** | Required fields, field-level errors, boundary values, format checks, max length/truncation, duplicate submission, special characters, XSS/script injection | N/A — this module has no user-input fields (it's a media browse-and-play experience, not a form) |
| **Navigation** | All header/footer links, breadcrumbs, back/forward browser buttons, tab order, in-page anchors, 404 handling, broken links | ✅ Done (Back button verified under real click-flow returns correctly to Summary; +Add entry point vs. direct deep-link both tested; Close (×) on both detail dialog and player tested) |
| **UI/UX** | Layout/alignment/spacing consistency, typography, color contrast vs design system, icon rendering, empty/loading/error/success states, hover/active/disabled states, modal/dropdown behavior, z-index/overlap, tooltips | ✅ Done (layout/spacing reviewed on desktop — clean and consistent across all 7 categories; touch-target sizes measured; **no completion/success state exists** — player silently closes on session end, see Bug #31; hover states not separately probed this pass) |
| **Responsive/Cross-device** | Desktop, tablet, mobile viewports; orientation change; touch target size; zoom | ✅ Done (mobile 390×844 tested — landing page reproduces the same global floating-widget overlap as Track Mood/Log Sleep; detail dialog and player render cleanly with no overlap since both are centered modals above the FAB layer; tablet/zoom/orientation-change not separately tested) |
| **Copy/Content** | Typos, grammar, tone consistency, placeholder text, truncated labels | ✅ Done (header, subtitle, all session titles/descriptions, button copy reviewed — no grammar issues found) |
| Cross-browser | Chrome, Firefox, Safari, Edge — latest + one version back | ⬜ Not done (single Chromium session only) |
| Accessibility (deep pass) | Keyboard-only navigation, focus order & visible focus ring, screen reader labels, heading hierarchy, measured color contrast (WCAG AA), skip links | 🔵 Partially — player keyboard operability tested and **passed** (Tab order correct, Enter/Space toggles Pause/Play — a notable improvement over Log Sleep's non-operable sliders, Bug #26); accessible names on all player controls verified correct; full focus-order across the entire library grid, contrast-ratio measurement, and screen-reader pass not done |
| Authentication/Authorization | Login/logout, session timeout, role-based access, unauthorized access attempts, token expiry | ⬜ Not done |
| Security | Input sanitization, HTTPS enforcement, CSRF, sensitive data exposure, secure cookies, rate limiting | ⬜ Not done |
| Performance | Page load time, API response times, behavior under slow network, large data sets | ⬜ Not done |
| Data integrity | Data persists after refresh/logout-login; concurrent edits; cross-module sync | ❌ **Failed** — completing a full session (Yoga Nidra, played to its natural `ended` event) does **not** persist anywhere: Mindful Minutes trend on Summary stayed "0 sec ↓", and no Meditation/Mindfulness card exists on the Diary page at all (see Bug #30) |
| Error handling | Network failure, API timeout/500, offline behavior, graceful degradation | ⬜ Not done |
| Notifications/Feedback | Toasts/snackbars for success/failure, badge counters | ❌ **Failed** — no toast, completion screen, or any feedback when a session finishes playing (see Bug #31) |
| Localization | Text expansion, date/number formats, RTL layout, untranslated strings | ⬜ Not done |
| Browser/session edge cases | Multiple tabs, back button after logout, expired session mid-action, cookies disabled | ⬜ Not done |
| Regression | Re-test related/adjacent features after a change | ⬜ Not done |

---
## Data reflection (explicitly requested this pass)
- **Summary → Trends → Mindful Minutes:** Did **not** update after completing a full 10:04 Yoga Nidra
  session (jumped to the final ~3 seconds via the underlying `<audio>` element and let the native
  `ended` event fire naturally, rather than waiting the full 10 minutes in real time — a legitimate way
  to trigger genuine session-completion logic without idling). Stayed at "0 sec ↓" even after a hard
  reload.
- **Diary:** No Meditation/Mindfulness card exists anywhere on the Diary page (Snapshot, Calorie
  Ledger, Sleep, Food Log, Intake, Distance, Activities, Vitals are all present) — meaning there is
  currently no visible way to confirm a meditation session was logged at all, on any screen.
- This is a marked contrast to Log Sleep and Track Mood, both of which correctly reflect in both
  Summary Trends and a Diary card (after a hard reload, consistent with the known stale-cache pattern
  from Bug #8).

## Bugs found this pass (Guided Meditation)
- Bug #30 — Completing a Guided Meditation session doesn't reflect anywhere: Mindful Minutes trend
  stays 0, no Diary card exists for meditation at all (P2, Functional/Data)
- Bug #31 — No completion/success feedback when a session finishes playing — player dialog silently
  closes with no toast, summary screen, or confirmation (P3, UX)
- Bug #32 — Touch targets under 44×44px: session dialog Close (×) at 30×30px, page-level "Back" button
  at 75×29px — consistent with the systemic pattern already logged across other Quick Add surfaces
  (P3, Accessibility)

## Notes/Doubts this pass (Guided Meditation)
- Rewind/Forward 10s buttons measure 43×43px — borderline under the 44px minimum but comparable to
  Log Sleep's slider handles (42×42px, judged acceptable there); not filed as a separate bug given the
  large surrounding tap area.
- Five session thumbnails (5 Senses, Body Scan, 20 Minute Compassion for Your Whole Body, 10 Minute
  Sleep Meditation, 12 Minute Sleep Gratitude Meditation) initially appeared as broken/blank in a
  full-page screenshot taken immediately after page load — verified this was a **lazy-load timing
  artifact only** (all 5 image URLs return HTTP 200, and all render correctly once scrolled into view
  or given a moment to load). Explicitly **not** filed as a bug after verification — flagging the
  investigation process here since it's a good example of "observation vs. verification" per the
  judgment rules.
- The player's keyboard accessibility (Tab order + Enter/Space toggling Pause/Play) is a genuine
  positive finding worth preserving — it's the most keyboard-accessible custom control found across
  the Mindfulness module so far (contrast with Bug #26 for Log Sleep's sliders).
- Player audio source is served from Cloudinary (`res.cloudinary.com/vantagecircle/.../Sessions/Yoga/yog_nidra.mp3`)
  — a third-party CDN dependency, noted for awareness but not a defect.

Full details in `dashboard/quick-add/bug-logs/bug-log.md`. Test cases in
`dashboard/quick-add/test-cases/guided-meditation.md`.
