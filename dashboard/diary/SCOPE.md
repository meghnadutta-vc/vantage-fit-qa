# Diary Page — Module / Element Testing Scope

**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary/diary`
**Account:** Demo/test tenant — CRUD is safe.

**Priority lens (per user guidance):** for every card/stat below, actively check for data-integrity
issues first — does it match Summary/other pages, does it update without a hard reload, is the
unit/value correct, does date navigation load the right day's real data — before checking
layout/a11y. Only flag layout defects that are concretely visible (real overlap/spacing), not bare
touch-target measurements. Known cross-page issues already logged elsewhere (Water unit mismatch —
Bug #34 quick-add; League banner mismatch — Bug #4 summary) should be cross-checked here for
consistency, not re-filed as new bugs unless the manifestation is meaningfully different.

## A. Page header & date navigation

| Element | Priority | Test status |
|---|---|---|
| "Diary" heading + "Today · <date>" subtitle | P4 | ✅ Done — matches real system date exactly |
| Date nav group: Previous day, "Today" label, Next day (disabled on today) | P1 | ✅ Done — correct; middle label is not interactive (no date-picker jump feature) |
| Navigating to a past day — does each card's data genuinely change to that day's real values | P1 | ✅ Done — all cards correct EXCEPT Water intake (Bug #1) |
| Navigating back to Today from a past day | P2 | ✅ Done — works correctly |
| Next day correctly stays disabled/blocked for any future date | P2 | ✅ Done — confirmed disabled on Today, enables on past days |

## B. Snapshot card (Diary's own copy)

| Element | Priority | Test status |
|---|---|---|
| Card wrapper — labeled "View Trends" here (vs. Summary's "Open Diary") — confirm where it navigates | P2 | ✅ Done — correctly navigates to `/ng/fit/activity-stats`, matching its label |
| Snapshot heading + info icon — check for the same redundant-duplicate-label issue found on Summary (Bug #3, summary area) | P3 | ✅ Done — reproduces the same pattern (not re-filed, same root cause) |
| Steps / Active Minutes values — must match Summary's Snapshot exactly (same day, same data) | P1 | ✅ Done — exact match confirmed |
| Note: this copy has no motivational text paragraph (Summary's does) — confirm intentional, not a rendering gap | P4 | ✅ Done — confirmed intentional, rest of card renders correctly |

## C. Calorie Ledger card

| Element | Priority | Test status |
|---|---|---|
| "Recommended" kcal value — plausibility/consistency across days | P2 | ✅ Done — stable 1,855 kcal across reloads today |
| Meals / Resting / Active / Balance breakdown — arithmetic check (Meals − Resting − Active = Balance, or whatever the real formula is) | P1 | ✅ Done — exact match every time; Resting's gradual live climb confirmed intentional |
| Deficit/surplus messaging text — correctness vs. actual balance sign | P2 | ✅ Done — "caloric deficit" message correctly matches negative Balance |
| "Learn more" link — destination and content | P3 | ✅ Done — opens genuine educational modal despite `href="#"` |
| Data reflection: does logging a meal change Meals/Balance immediately | P1 | ⛔ Blocked — meal logging is app-only (mobile hand-off), not testable from web |

## D. Food Log card

| Element | Priority | Test status |
|---|---|---|
| Empty state ("No food logged for this day") + add button | P2 | ✅ Done — button opens app-only hand-off modal, tested in Section C |
| Logging a meal (if reachable from here) — does it appear in the list, correct calorie value | P1 | ⛔ Blocked — mobile-only, not testable from web (see Section C) |
| Multiple meals — list rendering, ordering | P3 | ⛔ Blocked — same reason, no web-based way to log any meal |

## E. Sleep card

| Element | Priority | Test status |
|---|---|---|
| Empty state ("No Data" / "Track your sleep to see insights") + add button | P2 | ✅ Done — renders correctly |
| After logging sleep via +Add — does this card update, matching Bug #2 (summary)'s in-place-refresh pattern | P1 | ✅ Done — updates in-place immediately, no gap (contrasts favorably with Bug #2) |
| Logged sleep duration accuracy vs. what was entered | P1 | ✅ Done — 8h 0m entered, 8h 0m displayed, persisted through hard reload |

## F. Intake card (Water/Calories)

| Element | Priority | Test status |
|---|---|---|
| "Log water" button — opens correct modal | P2 | ✅ Done — opens correctly, but shows a wrong pre-filled value (see Bug #1) |
| Calories row — accuracy | P2 | ✅ Done — correctly 0kcal, matches 0 meals logged |
| Water row — cross-check against known Bug #34 (quick-add area: wrong unit "25.36/ 2.5 L") — confirm still reproducing here | P1 | ✅ Done — reproduces, and escalated to P1 with raw API evidence (Bug #1) |

## G. Distance card

| Element | Priority | Test status |
|---|---|---|
| Unit label ("mile"/"km") — toggle if available, consistency with other unit displays elsewhere | P2 | ✅ Done — plain static text, no toggle exists |
| Moved / Jog-Run / Cycling rows — empty state ("—") vs. populated accuracy | P2 | ✅ Done — consistently empty across all days tested, including a day with 7 logged activities; appears to be a separate (device-based) data source, not a bug |

## H. Activities card

| Element | Priority | Test status |
|---|---|---|
| "N logged" counter accuracy vs. actual list length | P1 | ✅ Done — accurate across all 3 days tested |
| Each activity row: name, time, duration, distance — accuracy vs. what was logged | P1 | ✅ Done — fully accurate |
| Clicking a logged activity — edit/delete affordance (known prior gap from quick-add testing — confirm still true) | P2 | ✅ Done — confirmed still no affordance, matches known gap |
| List ordering (chronological?) with 2+ activities | P3 | ✅ Done — 14 July's 3 visible items (Hiking/Swimming/Yoga) render in chronological order |

## I. Vitals card (Mood / Heart Rate / Weight)

| Element | Priority | Test status |
|---|---|---|
| Mood row: "--" empty state, "Log mood" button, correct value + "Edit mood" label after logging | P1 | ✅ Done — pre-fill and label both correct |
| Heart Rate row: "--" empty state, "Log heart rate on the app" — confirm this is intentionally app-only (mobile hand-off) | P2 | ✅ Done — confirmed app-only label, consistent with other hand-off patterns |
| Weight row: "--" empty state, "Log weight" button, correct value + "Edit weight" label after logging | P1 | ✅ Done — pre-fill ("Same as last log") and label both correct |
| Cross-check: does Vitals reflect the same data shown on Summary's Vitals card (no mismatch) | P1 | ✅ Done — Weight matches exactly, no discrepancy found |

## J. Cross-cutting checks (apply to every card above)

- Responsive: desktop (1440×900) and mobile (390×844)
- Data reflection: does each card match Summary's equivalent value; does it update without a hard reload after logging via +Add
- Accessibility: labels on icon-only buttons (Food Log's add button, Sleep's add button), focus order, contrast
- Loading/error states on initial page load (slow network, failed API call)
- Grammar/copy review across all card headings, empty states, and helper text

---
Status legend: ⬜ Pending · 🔵 In progress · ✅ Done · ⛔ Blocked · ⏸️ Held back
