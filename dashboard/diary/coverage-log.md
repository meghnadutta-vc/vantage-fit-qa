# Diary Page — Coverage Log

## 2026-07-15 — Section A: Page header & date navigation
- **Status:** ✅ Done
- Tested: "Diary" heading/date subtitle accuracy, Previous/Next day navigation across 3 consecutive
  days (13/14/15 July), Next day disabled-on-Today state, date-picker-jump affordance (none found),
  and cross-checked every card's data accuracy per day (Snapshot, Calorie Ledger, Sleep, Intake,
  Activities, Vitals).
- 1 bug found (#1 — Water intake shows a stale cross-day value on Today despite no water logged,
  confirmed server-side via hard reload).
- Positive: every other card (Steps/Active Minutes, Calorie Ledger, Sleep, Activities, Vitals)
  correctly reflects each day's real historical data when navigating — only Water fails this check.

## 2026-07-15 — Section B: Snapshot card (Diary's own copy)
- **Status:** ✅ Done
- Tested: card wrapper navigation destination, redundant-focus-stop check (DOM inspection), Steps/
  Active Minutes cross-check against Summary's Snapshot, motivational text absence.
- 0 new bugs (1 reproduction noted — same root cause as Summary area's Bug #3, not re-filed).
- Positive: this card's "View Trends" label correctly matches its destination (`/ng/fit/activity-
  stats`), unlike Summary's version; Steps/Active Minutes match Summary exactly for the same day.

## 2026-07-15 — Section C: Calorie Ledger
- **Status:** ✅ Done
- Tested: arithmetic verification (Meals − Resting − Active = Balance), Resting kcal stability/growth
  pattern over time, "Learn more" link, meal-logging entry point (app-only hand-off) and its
  focus-trap behavior.
- 0 new bugs.
- Positive: arithmetic exact; Resting kcal's gradual climb confirmed as intentional live RMR
  tracking, not an error; "Learn more" opens genuine educational content despite `href="#"`.
- Not testable from web: meal logging itself (mobile-only), so Meals/Balance in-place-update
  behavior after a real meal log could not be directly verified this pass.

## 2026-07-15 — Section D: Food Log
- **Status:** ✅ Done (blocked items noted, not skipped silently)
- Covered by Section C's testing of the "Log meals" button (same control) — confirmed app-only
  hand-off, empty state renders correctly, focus-trap pattern reproduces.
- 0 new bugs. Meal-list rendering/ordering blocked — no web-based way to log any meal to populate it.

## 2026-07-15 — Section E: Sleep card
- **Status:** ✅ Done
- Tested: empty state, logging sleep via the card's own "Add Sleep Data" button (real web modal),
  in-place update behavior, persistence via hard reload.
- 0 bugs found — genuinely positive result.
- Positive: Sleep card updates immediately in-place after saving (no reload/nav-away needed),
  confirmed persisted through a hard reload — contrasts favorably with Bugs #2/#4 in the Summary area.

## 2026-07-15 — Section F: Intake (Water/Calories)
- **Status:** ✅ Done
- Tested: Calories row accuracy, Log Water modal contents, and escalated Bug #1 with hard evidence
  by inspecting the raw `POST /vantagefit/api/v1/today/overview` API response.
- Escalated Bug #1 from P2 to **P1** — confirmed via raw API response body that the backend itself
  (not just the UI) misattributes 14 July's real water log to Today, both in the aggregate nutrition
  total AND in today's individual activities feed (a "Water (11:55 AM)" entry appears in today's
  feed that was never actually logged today).
- New finding within Bug #1: the Log Water modal's own pre-fill shows a THIRD, different, larger
  wrong value ("5000 ml / 20 of 8 glasses / Daily goal reached") that doesn't reconcile with the
  25.36 fl oz / 750 ml figure found in the API — flagged for separate dev investigation.
- Positive: Calories row correctly shows 0kcal, consistent with 0 meals logged today.

## 2026-07-15 — Section G: Distance
- **Status:** ✅ Done
- Tested: unit label interactivity, Moved/Jog-Run/Cycling rows across 3 days (13/14/15 July,
  including a day with 7 logged activities).
- 0 bugs found.
- Observation (not a bug): this card appears to track a separate data source (device/GPS-based)
  from manually-logged Activities, consistently empty on this demo account — not evidence of a defect.

## 2026-07-15 — Section H: Activities
- **Status:** ✅ Done
- Tested: "N logged" counter accuracy across 3 days, individual row data accuracy, "View all" button,
  edit/delete affordance on a logged row.
- 0 new bugs. Reproduces the known "View all" bug (quick-add area's #9) from this Diary entry point;
  no edit/delete affordance found (known scope gap, not new).
- Positive: counter and row data both fully accurate against real logged data.

## 2026-07-15 — Section I: Vitals
- **Status:** ✅ Done
- Tested: Edit mood pre-fill/label, Edit weight pre-fill/label, cross-page consistency vs. Summary's
  Vitals card, Heart Rate app-only confirmation.
- 0 bugs found — clean section.
- Positive: both Mood and Weight edit flows correctly pre-fill and label themselves; Weight matches
  Summary's Vitals card exactly (no cross-page inconsistency, unlike Bugs #1/#4 elsewhere).
