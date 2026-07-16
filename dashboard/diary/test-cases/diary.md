# Diary — Test Cases

**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary/diary`
**Account:** Demo/test tenant — CRUD is safe.
**Structure:** One consolidated file for the whole Diary module. Each section below is one card/area
of the page, with Functional & Data-Integrity / UI-UX & Content / Accessibility tables where
applicable. Within each table: most-important cases first, general cases in the middle, corner/edge
cases at the end. The module's smoke suite lives separately in the shared
`dashboard/smoke/diary-smoke.md` folder (common across all dashboard modules), not in this file.

**Branching policy:** a case is split into `a`/`b`/`c` siblings only when the expected result itself
qualitatively changes by ambient account state (different button label, different pre-fill text, or a
known bug's specific signature). Otherwise, one row with a dynamic assertion that reads whatever the
actual state is at run time — see `dashboard/smoke/diary-smoke.md`'s header note for the full rationale.

Status legend: ⬜ Pending · 🔵 In progress · ✅ Done — legend applies to the Progress tracker below, not
to individual test-case rows (Actual Result/Status columns in each table are filled by the human QA).

## Progress tracker

| Section | Status |
|---|---|
| A. Header & Date Navigation | ✅ Done |
| B. Snapshot card | ✅ Done |
| C. Calorie Ledger card | ✅ Done |
| D. Food Log card | ✅ Done |
| E. Sleep card | ✅ Done |
| F. Intake card (Water/Calories) | ✅ Done |
| G. Distance card | ✅ Done |
| H. Activities card | ✅ Done |
| I. Vitals card (Mood/Heart Rate/Weight) | ✅ Done |
| J. Footer / global chrome | ✅ Done |
| K. Cross-date regression (multi-card, multi-day sweep) | ✅ Done |
| L. Responsiveness (desktop/laptop/iPad/mobile) | ✅ Done |
| M. Global accessibility (page-level, not card-specific) | ✅ Done |

---

## A. Header & Date Navigation

**Scope:** the "Diary" heading, date subtitle, and Previous/Today-label/Next date-nav control group.
Full per-card multi-date data verification (does every card's *value* stay accurate across many days)
is covered in Section K — this section owns the nav mechanism itself: does it move correctly, lock
correctly, survive edge dates, and behave well for keyboard/screen-reader users.

### A.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-HDR-TC-001 | "Diary" heading and date subtitle match the real system date on load | Fresh page load, Today | 1. Load `/ng/fit/summary/diary` 2. Read heading and subtitle 3. Compare subtitle against `new Date()` on the same machine | Heading reads "Diary"; subtitle exactly matches today's real date (day, month, year) — no off-by-one, no timezone drift | | | P1 |
| DIARY-HDR-TC-002 | Previous day navigates back exactly one calendar day | On Diary page, Today | 1. Note current date subtitle 2. Click "Previous day" once | Subtitle now shows exactly yesterday's date; no card is stuck in a stale/loading state after the transition | | | P1 |
| DIARY-HDR-TC-003 | Next day is disabled precisely on Today, and only on Today | On Diary page, Today | 1. Confirm "Next day" is disabled on Today 2. Click "Previous day" once 3. Confirm "Next day" is now enabled | Disabled exactly when viewing Today; enabled on every day before Today | | | P1 |
| DIARY-HDR-TC-004 | A 3-day consecutive navigation sweep shows each day's own real data | On Diary page, 3 consecutive days with known distinct data (e.g. differing Active Minutes/Activities count) | 1. Load Today 2. Click "Previous day" twice, recording each day's Steps, Active Minutes, Activities count, Sleep, Vitals along the way | Each of the 3 days shows its own distinct, correct historical values — no day repeats another day's card values (except where two days genuinely, coincidentally match) | | | P1 |
| DIARY-HDR-TC-005 | Multi-hop return to Today restores the exact original baseline | On Diary page, navigated 3+ days back | 1. Click "Next day" repeatedly until back on Today 2. Compare every card against the Today baseline captured before navigating away | Header subtitle and every card's values exactly match the pre-navigation Today baseline; "Next day" re-disables correctly | | | P1 |
| DIARY-HDR-TC-006 | Rapid repeated clicks on "Previous day" land on the correct day with no race condition | On Diary page, Today | 1. Click "Previous day" 5 times in quick succession (as fast as the UI allows) 2. Read final subtitle | Subtitle shows exactly 5 calendar days before today — not 4 or 6 (no dropped/double-counted click), and no intermediate day's data flashes incorrectly or gets stuck mid-transition | | | P2 |
| DIARY-HDR-TC-007 | Date navigation correctly crosses a month boundary | On Diary page, navigate to the 1st of the current month | 1. From the 1st, click "Previous day" once | Subtitle correctly shows the last day of the prior month (e.g. "30 June 2026" from "1 July 2026"), not "0 July" or a broken date | | | P2 |
| DIARY-HDR-TC-008 | Date navigation correctly crosses a year boundary | On Diary page, navigate to 1 January of the current year | 1. From 1 January, click "Previous day" once | Subtitle correctly shows 31 December of the prior year, with the year number correctly decremented | | | P2 |
| DIARY-HDR-TC-009 | Date navigation correctly handles a leap-day (29 Feb) without crashing | On Diary page | 1. Navigate back to a date range spanning 29 Feb of the most recent leap year (2024) — i.e. land on 1 Mar 2024 and click "Previous day" | Subtitle correctly shows "29 February 2024"; page does not crash/blank, card data loads normally (populated or empty state, no error) | | | P3 |
| DIARY-HDR-TC-010 | Navigating far back in time (well before any real account data) degrades gracefully | On Diary page | 1. Navigate back roughly 1 year from today (well beyond any known logged data) | Page loads without error; every card shows a valid empty state, not an infinite spinner or a raw error | | | P2 |
| DIARY-HDR-TC-011 | Midnight rollover — "Today" recognizes the new day without a manual reload | Page left open across a real midnight transition (or system clock advanced past midnight in a test env) | 1. Leave the Diary page open on Today just before midnight 2. Wait until after midnight (or advance clock) 3. Without reloading, check whether the header/subtitle and "Next day" disabled-state update to the new day | **Note/Doubt, not a confirmed bug without direct verification:** determine whether the page auto-updates to the new "Today" or requires a manual reload/re-navigation to recognize the date has changed — log actual behavior, flag as a bug only if a reload is required and the app doesn't itself prompt for one | | | P3 |
| DIARY-HDR-TC-012 | Middle date-nav label is confirmed non-interactive (no date-picker jump exists) | On Diary page, any day | 1. Click/attempt to activate the middle label (e.g. "Today", "14 Jul") | Label is plain text — no click handler fires, no date picker opens; confirms this is a scope limitation, not a broken feature | | | P3 |
| DIARY-HDR-TC-013 | Browser back/forward interaction with date-nav state | On Diary page, Today | 1. Click "Previous day" twice 2. Press the browser's Back button 3. Press Forward | Document and log the actual behavior: does Back undo one day-step (SPA history state), reload the whole page, or navigate away from Diary entirely? No crash in any case — flag as a Note/Doubt if the behavior is confusing (e.g. Back exits Diary instead of undoing one day) rather than assuming a specific "correct" behavior, since no spec confirms which is intended | | | P2 |
| DIARY-HDR-TC-014 | URL reflects (or doesn't) the selected date — bookmarkability check | On Diary page, navigated to a past day | 1. Read the browser URL after navigating to a past day 2. If it contains a date param/segment, copy it and load it in a fresh tab | If the URL encodes the date: fresh-loading it lands directly on that date. If it does NOT encode the date: this is a scope gap (no deep-linking/bookmarking to a specific day) — log as a Note/Doubt or Enhancement, not a bug, since no requirement confirms this should exist | | | P3 |
| DIARY-HDR-TC-015 | Next-day lock does not falsely trigger for any past date | On Diary page, navigated to any day before Today | 1. On each of several past days visited, confirm "Next day" is enabled (never falsely disabled on a non-Today past day) | "Next day" is enabled on every day strictly before Today, with no false-disable | | | P2 |

### A.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-HDR-TC-016 | Date subtitle FORMAT consistency vs. Summary page's date subtitle | Diary and Summary both loaded | 1. Read Diary's subtitle format (e.g. "Today · 15 July 2026") 2. Read Summary's subtitle format (e.g. "Tuesday, 14 July 2026") | **Judgment call, not an automatic bug:** the two pages use different date formats (no day-of-week + "·" separator on Diary vs. day-of-week + comma on Summary) within the same design system. Flag as a P3 UI inconsistency worth a design decision — either both should match, or the difference should be confirmed intentional (e.g. Diary's format exists because it needs to show "Today" contextually, which a bare weekday name couldn't do) | | | P3 |
| DIARY-HDR-TC-017 | Middle nav label's short-date format vs. subtitle's long-date format | On Diary page, navigated to a past day | 1. Compare the middle label's format (e.g. "14 Jul", no year) against the subtitle's format above it (e.g. "14 July 2026", full year) | Both are legitimate short-form vs. long-form displays of the same date — confirm no year ambiguity if a user navigates more than one year back (does the short label ever need a year to disambiguate?); flag as a Note/Doubt if navigating across a year boundary makes the short label ambiguous | | | P4 |
| DIARY-HDR-TC-018 | "Diary" heading typography matches other page headings (design-system consistency) | Diary and Summary/other module pages loaded | 1. Compare font size/weight/color of "Diary" heading against "Summary" heading and other module headings | Typography (size, weight, color) is consistent across all top-level page headings — any deviation is a P3 design-system finding | | | P3 |
| DIARY-HDR-TC-019 | Disabled "Next day" button has a visually distinct disabled state | On Diary page, Today | 1. Visually inspect "Next day" button styling on Today vs. its styling on a past day (enabled) | Disabled state is clearly visually distinct (reduced opacity/grayed out/no hover feedback) from the enabled state — not just non-functional while looking identical to enabled | | | P3 |
| DIARY-HDR-TC-020 | Date-nav icon buttons (Previous/Next) alignment and spacing | On Diary page | 1. Inspect spacing/alignment of Previous icon, middle label, Next icon as a group | Group is visually balanced/centered per design system, consistent spacing on both sides of the label, no visual crowding or misalignment | | | P4 |
| DIARY-HDR-TC-021 | Grammar/copy review of all header/date-nav text | On Diary page, across several days | 1. Read every string in this area: heading, subtitle, middle label variants | No typos, no grammatical errors, consistent capitalization style (e.g. "Today" vs "today") | | | P4 |
| DIARY-HDR-TC-022 | Long/localized date strings do not overflow or truncate awkwardly | On Diary page, narrow viewport (see also Section L — Responsiveness) | 1. Resize to mobile width 2. Check subtitle rendering for a long date string | Full date string remains fully visible and legible, no clipping/ellipsis cutting off part of the date — cross-reference with Section L if this needs deeper multi-breakpoint testing | | | P3 |

### A.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-HDR-TC-023 | Previous/Next day buttons have accessible names, not just bare icons | On Diary page | 1. Inspect DOM/accessibility tree for both buttons | Each has a real accessible name (e.g. `aria-label="Previous day"` / `"Next day"`), not an unlabeled icon-only element | | | P1 |
| DIARY-HDR-TC-024 | Previous/Next day are keyboard-operable without a mouse | On Diary page | 1. Tab to "Previous day" 2. Activate with Enter/Space 3. Repeat for "Next day" | Both controls are reachable via Tab and activate correctly via keyboard, matching mouse-click behavior | | | P1 |
| DIARY-HDR-TC-025 | Disabled "Next day" is programmatically disabled, not just visually | On Diary page, Today | 1. Inspect the DOM attribute/state of "Next day" while on Today | Element has `disabled` or `aria-disabled="true"` — assistive tech correctly announces it as unavailable, not as a clickable no-op | | | P2 |
| DIARY-HDR-TC-026 | Date change is announced to assistive technology | On Diary page, screen reader active (or DOM inspection for `aria-live`) | 1. Click "Previous day" 2. Check whether an `aria-live` region (or equivalent) announces the new date/updated content | **Likely gap, verify don't assume:** since this is a silent SPA content swap (no page reload/navigation), a screen-reader user may get no announcement that the date and all card data just changed. If no `aria-live` region exists, log as a P2 accessibility finding — a real, concrete gap, not a bare touch-target-style nitpick | | | P2 |
| DIARY-HDR-TC-027 | Focus is preserved sensibly after a date-nav click | On Diary page | 1. Click "Previous day" 2. Check `document.activeElement` | Focus remains on (or sensibly returns to) the "Previous day" button — not lost to `<body>`, which would disorient keyboard users mid-task | | | P3 |
| DIARY-HDR-TC-028 | Touch target size for Previous/Next day icons | On Diary page, mobile viewport | 1. Measure tappable area of both icon buttons | Meets ≥44×44px (iOS) / ≥48×48dp-equivalent (Android parity) minimum — log only if there's a concrete, visible tap-reliability problem, not as a standalone measurement nitpick | | | P4 |
| DIARY-HDR-TC-029 | Color contrast of header text and date-nav icons | On Diary page | 1. Check contrast ratio of heading text, subtitle text, and icon colors against their background | Meets WCAG AA (4.5:1 text, 3:1 for icons/UI components) | | | P3 |

---

## B. Snapshot card

**Scope:** Diary's own copy of the Snapshot card (Steps / Active Minutes, progress rings, "View
Trends" navigation). Known context: this card reuses the same wrapper component as Summary's Snapshot
card, which has a confirmed redundant-tab-stop pattern (Summary's Bug #3) and a known stale-data gap
after logging (Summary's Bug #2) — both are re-verified here against Diary's copy specifically, since
a shared component doesn't guarantee identical behavior in every context.

### B.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SNAP-TC-001 | Steps value is accurate against the backing API data | On Diary page, Today, known step count for the day | 1. Read Steps value/goal on Snapshot card 2. Inspect the `today/overview` API response's Steps field | Displayed value exactly matches the API's raw Steps value and goal — no rounding/unit discrepancy | | | P1 |
| DIARY-SNAP-TC-002 | Active Minutes value is accurate against the backing API data | On Diary page, Today, known Active Minutes for the day | 1. Read Active Minutes value/goal on Snapshot card 2. Inspect the `today/overview` API response's Active Minutes field | Displayed value exactly matches the API's raw value and goal | | | P1 |
| DIARY-SNAP-TC-003 | Progress percentage/ring math is correct for both stats | On Diary page, a day with non-zero, non-goal Steps and Active Minutes | 1. Read displayed % for Steps and Active Minutes 2. Independently compute value/goal × 100 (rounded) for each | Displayed % exactly matches the computed value for both stats; ring fill visually corresponds to the same % | | | P1 |
| DIARY-SNAP-TC-004 | Logging a new workout updates Steps/Active Minutes on this card | On Diary page, Today; note current Steps/Active Minutes as baseline | 1. Log a new workout via Quick Add (e.g. "Running", 30 min) 2. Without a hard reload, return to/stay on the Diary page 3. Re-check Snapshot's Steps and Active Minutes | **Verify, don't assume:** does the card update in place (matching Sleep's good in-place-refresh pattern) or does it require a nav-away-and-back / hard reload to reflect the new workout (matching Summary's known Bug #2 stale-Snapshot gap)? Log the actual behavior; only file as a NEW bug if it requires a full hard reload (nav-away-and-back already being a known, previously-documented gap elsewhere) | | | P1 |
| DIARY-SNAP-TC-005 | After logging a workout (per TC-004), the updated values persist through a hard reload | Following TC-004 | 1. Hard-reload the Diary page 2. Re-check Steps/Active Minutes | Both values reflect the newly-logged workout accurately post-reload, confirming server-side persistence regardless of the in-place-update behavior found in TC-004 | | | P1 |
| DIARY-SNAP-TC-006 | Multiple workouts logged in the same day accumulate correctly | On Diary page, Today | 1. Log a second distinct workout (e.g. "Cycling", 20 min) after TC-004/005 2. Hard reload 3. Check Active Minutes | Active Minutes reflects the cumulative total of all workouts logged today (not just the latest one, not double-counted) | | | P1 |
| DIARY-SNAP-TC-007 | Card wrapper navigates to the correct destination | On Diary page, any day | 1. Click the Snapshot card (or its "View Trends" affordance) | Navigates to `/ng/fit/activity-stats` — correctly matching its "View Trends" label (in contrast to Summary's mislabeled equivalent) | | | P2 |
| DIARY-SNAP-TC-008 | Steps/Active Minutes match Summary's Snapshot exactly for the same day | Diary and Summary both loaded for the same date | 1. Note Diary's Snapshot values 2. Navigate to Summary, note its Snapshot values | Identical value, goal, and % on both pages — holds for both zero and non-zero states | | | P1 |
| DIARY-SNAP-TC-009 | Snapshot values stay correct when navigating to a past day | On Diary page, navigate to a day with different known Steps/Active Minutes than Today | 1. Navigate to that day 2. Read Snapshot values | Values update to that day's own real historical data, matching what was logged on that specific day (cross-check with Section K's multi-date sweep) | | | P1 |
| DIARY-SNAP-TC-010 | Zero-data state renders correctly | On Diary page, a day with 0 Steps and 0 Active Minutes logged | 1. Load that day 2. Read Snapshot card | Renders as "0/<goal>", 0%, empty/unfilled progress ring — no error, no broken layout, no div-by-zero artifact | | | P2 |
| DIARY-SNAP-TC-011 | Goal-exceeded state (>100%) renders sensibly | On Diary page, a day where Steps or Active Minutes exceeds its goal | 1. Load that day 2. Read the % and ring fill | Percentage and ring handle the >100% case sensibly (e.g. caps ring fill visually at 100% while the numeric % may still show the true value, or both cap — whichever the design intends); flag as a Note/Doubt if the ring overflows/clips oddly rather than assuming a specific correct behavior | | | P3 |
| DIARY-SNAP-TC-012 | Extremely large step count does not break layout | On Diary page (may require a day with an unusually high count, or Note/Doubt if not reproducible with real data) | 1. Load a day with a very high Steps value (5-6 digits) if available 2. Inspect rendering | Number renders fully, no text overflow/clipping/layout break; if no such day is available to test, log as untested with a note rather than assuming pass | | | P4 |

### B.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SNAP-TC-013 | "Snapshot" heading and info icon — duplicate-affordance check | On Diary page | 1. Inspect the info icon next to "Snapshot" heading 2. Compare its behavior/label to the card's own "View Trends" action | **Reproduction check, not a new bug:** confirm whether this icon is actually a duplicate "View Trends" chevron (same root cause as Summary's Bug #3) rather than a distinct info/tooltip affordance — log as a reproduction of the existing shared-component issue, don't re-file | | | P3 |
| DIARY-SNAP-TC-014 | No motivational text paragraph — confirm intentional simplification | Diary and Summary Snapshot cards both loaded | 1. Compare Diary's Snapshot card content to Summary's (which includes "You have been among the top X%...") | Diary's copy intentionally omits this paragraph; card layout is complete and correct without it (not a rendering gap) — Enhancement-level observation only, not a bug | | | P4 |
| DIARY-SNAP-TC-015 | Number formatting consistency (thousands separators, unit spacing) | On Diary page, a day with a 4+ digit Steps value | 1. Read the Steps value format (e.g. "10,000" vs "10000") | Consistent thousands-separator formatting matching the design system's convention elsewhere on the page | | | P4 |
| DIARY-SNAP-TC-016 | Progress ring color/fill matches design-system conventions | On Diary page | 1. Compare ring color/style to progress indicators elsewhere in the app (e.g. Trends widget bars) | Consistent color palette and fill-direction convention across all progress indicators in the design system | | | P3 |
| DIARY-SNAP-TC-017 | Card spacing/alignment consistent with adjacent cards (Calorie Ledger, etc.) | On Diary page | 1. Compare padding/margins/heading style of Snapshot card against neighboring cards | Consistent spacing and typography treatment across all Diary cards | | | P4 |
| DIARY-SNAP-TC-018 | Grammar/copy review of stat labels | On Diary page | 1. Read "Steps", "Active Minutes" labels and any tooltip text | No typos, consistent capitalization/terminology with the same stats shown elsewhere (Summary, Trends) | | | P4 |

### B.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SNAP-TC-019 | Card wrapper does not create a redundant/confusing tab stop | On Diary page | 1. Tab through the Snapshot card via keyboard 2. Inspect DOM: outer `role="button"` wrapper vs. inner real `<button>` | **Reproduction check:** confirm both the outer wrapper and inner chevron button are independently focusable with identical/near-identical accessible names ("View Trends") — a real redundant-stop issue for screen-reader/keyboard users, same root cause as Summary's Bug #3, not re-filed as new | | | P3 |
| DIARY-SNAP-TC-020 | Progress percentage is available as text, not conveyed by ring color/fill alone | On Diary page | 1. Inspect DOM/accessibility tree for the progress ring | The numeric value/% is present as real text content (not purely a decorative SVG/canvas with no text equivalent) — usable for screen-reader and color-blind users | | | P2 |
| DIARY-SNAP-TC-021 | Color contrast of stat text and ring against card background | On Diary page | 1. Check contrast ratio of Steps/Active Minutes numeric text and labels | Meets WCAG AA (4.5:1 text) | | | P3 |
| DIARY-SNAP-TC-022 | Touch target size of the card / chevron | On Diary page, mobile viewport | 1. Measure tappable area of the card and its chevron icon | Meets ≥44×44px minimum — log only if there's a concrete tap-reliability issue, not as a standalone measurement nitpick | | | P4 |

## C. Calorie Ledger card

**Scope:** Recommended kcal, Meals/Resting/Active/Balance breakdown, deficit/surplus messaging,
"Learn more" educational modal. **Known constraint:** actual meal *logging* (the "Log meals" button,
shared with the Food Log card) opens a "Continue this in the Vantage Fit app" mobile hand-off modal —
there is no real web form for entering a food item. This means positive/negative/edge-case testing of
food-item values (extreme calorie counts, decimals, 0-kcal items, malformed quantities, etc.) **cannot
be executed from the web dashboard at all** — every case that needs a specific logged meal value is
marked BLOCKED below with the exact scenario documented, so it can be picked up by Android QA
(`android/ui-ux/`) or web automation can skip it without silently losing the scenario.

### C.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-CAL-TC-001 | Arithmetic holds: Meals − Resting − Active = Balance | On Diary page, any day with non-zero Resting/Active | 1. Read Meals, Resting, Active, Balance 2. Compute Meals − Resting − Active | Computed value exactly equals displayed Balance, allowing for Resting's intentional live climb at the moment of reading | | | P1 |
| DIARY-CAL-TC-002 | Balance sign correctly drives deficit/surplus messaging | On Diary page, a day with a negative Balance (deficit) | 1. Read Balance sign 2. Read the messaging text below it | Negative Balance → message correctly says "caloric deficit" (or equivalent); the sign and the wording never contradict each other | | | P1 |
| DIARY-CAL-TC-003 | Deficit/surplus messaging flips correctly on a surplus day | On Diary page, a day with a positive Balance (surplus) — if none exists in test data, log as Note/Doubt/untested rather than assuming | 1. Read Balance sign 2. Read the messaging text | Positive Balance → message correctly says "caloric surplus" (or equivalent) — must be independently verified with real surplus data, not assumed from the deficit case alone | | | P1 |
| DIARY-CAL-TC-004 | Zero-balance edge case shows neutral (non-deficit/non-surplus) messaging | On Diary page, a day where Meals − Resting − Active = 0 exactly (may need to be engineered via a controlled log, or Note/Doubt if unreachable with real data) | 1. Read Balance and messaging | Messaging does not falsely claim a deficit or surplus when Balance is exactly 0 — some neutral wording (or the messaging is suppressed) is expected; flag as untested with a note if this exact state can't be produced | | | P3 |
| DIARY-CAL-TC-005 | Calorie Ledger values match Diary's own selected date when navigating | On Diary page, navigate to a day with different known Resting/Active values | 1. Navigate to that day 2. Read Meals/Resting/Active/Balance | Values update to that day's own real historical data (cross-check against Section K's multi-date sweep) | | | P1 |
| DIARY-CAL-TC-006 | "Recommended" kcal value is stable across reloads within the same day | On Diary page, Today | 1. Note Recommended value 2. Hard reload 2–3 times | Value stays identical across reloads (not recalculating randomly on every page load) | | | P2 |
| DIARY-CAL-TC-007 | "Recommended" kcal value's plausibility across different days | On Diary page, 2 distinct days | 1. Compare Recommended value on two different days | Value is either stable (same static per-user target) or changes for a plausible, explainable reason (e.g. a logged weight change) — flag as a Note/Doubt if it changes with no apparent cause | | | P3 |
| DIARY-CAL-TC-008 | Resting kcal climbs smoothly and monotonically over the course of a day | On Diary page, Today | 1. Note Resting value 2. Wait ~15–20 minutes without navigating away 3. Re-check Resting value | Value has increased, and the increase is smooth/plausible for a live RMR calculation (not a random jump, not a decrease) — confirms intentional live-tracking, not a bug | | | P2 |
| DIARY-CAL-TC-009 | "Learn more" opens correct educational content | On Diary page | 1. Click "Learn more" 2. Read modal content 3. Close via X, Escape, and click-outside (3 separate attempts) | A genuine "Caloric Balance"/RMR educational modal opens (despite `href="#"`) with accurate, well-written content; all three close methods work | | | P2 |
| DIARY-CAL-TC-010 | "Learn more"'s `href="#"` does not cause an unwanted scroll-jump | On Diary page, scrolled partway down | 1. Scroll to the middle of the page 2. Click "Learn more" | Page does not jump to the top (a common side effect of unhandled `href="#"` anchors) before the modal opens | | | P3 |
| DIARY-CAL-TC-011 | Zero-meals day shows "0 kcal" for Meals, not blank | On Diary page, a day with 0 meals logged | 1. Read Meals row | Displays "0 kcal" explicitly, not an empty/blank field | | | P2 |
| DIARY-CAL-TC-012 | [BLOCKED — app-only] Logging a meal updates Meals/Balance in place on web | On Diary page, Today | 1. Attempt to log a meal directly from the web (via "Log meals") | Cannot be executed — button opens a mobile hand-off modal, no real web form exists. **Scenario to hand off to Android QA:** log a meal on the app, then reload this web page without logging anything further on web, and confirm Meals/Balance reflect the app-logged meal correctly and promptly (tests cross-platform sync latency, not just in-app display) | | | P1 — untestable from web, needs cross-platform coordination |
| DIARY-CAL-TC-013 | [BLOCKED — app-only] Positive/negative/edge food-item values change Meals correctly | N/A — no web entry point | Cannot be executed from web. **Scenarios to hand off to Android QA:** (a) a very high-calorie single item (e.g. 5,000+ kcal) — does Meals/Balance handle it without overflow/clipping; (b) a 0-kcal logged item (e.g. water-only "meal") — does it still count as "1 meal logged" correctly; (c) decimal-calorie values if the app allows manual entry; (d) an item with an unusually long name — list/UI rendering on this Diary page once synced | Documented for Android QA follow-up; not a web-testable gap, not silently dropped | | | P2 — untestable from web |

### C.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-CAL-TC-014 | Deficit/surplus messaging copy — tone and clarity | On Diary page | 1. Read the exact deficit/surplus sentence(s) | **Judgment call:** wording is clear, factually framed (not alarmist/judgmental about the user's calorie balance) — note as an opinion (correct/incorrect/judgment call) rather than a blanket bug if the tone is merely debatable | | | P4 |
| DIARY-CAL-TC-015 | Number/unit formatting consistency (kcal, thousands separators) | On Diary page, a day with a 4-digit Resting/Active value | 1. Read formatting of all four values | Consistent thousands-separator and unit-label formatting matching the rest of the page (e.g. Snapshot's Steps formatting) | | | P4 |
| DIARY-CAL-TC-016 | "Learn more" is styled/semantically appropriate for its function | On Diary page | 1. Inspect whether "Learn more" is a real link/button element or a styled `<span>`/`<div>` | Uses a real interactive element (`<a>` or `<button>`) with appropriate semantics, not a non-semantic clickable div | | | P3 |
| DIARY-CAL-TC-017 | Card spacing/typography consistent with Snapshot and other Diary cards | On Diary page | 1. Compare heading style, padding, row spacing against Snapshot/other cards | Consistent design-system treatment across cards | | | P4 |
| DIARY-CAL-TC-018 | Grammar/copy review of all Calorie Ledger text | On Diary page | 1. Read every label and the Learn More modal's content | No typos/grammatical errors, consistent terminology with Summary/Trends (e.g. "Active" calories vs. "Active Minutes" elsewhere — confirm no confusing overlap in terminology) | | | P4 |

### C.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-CAL-TC-019 | "Learn more" is keyboard-operable | On Diary page | 1. Tab to "Learn more" 2. Activate with Enter/Space | Opens the modal identically to a mouse click | | | P2 |
| DIARY-CAL-TC-020 | "Learn more" modal has no focus trap (reproduction of known hand-off-modal gap) | On Diary page | 1. Open "Learn more" 2. Tab repeatedly | **Reproduction check:** confirm whether focus is trapped within the modal (correct) or leaks out to background page elements (known gap pattern reproduced elsewhere, e.g. Quick Add's Bug #3) — same root cause if reproduced, not a new bug number | | | P2 |
| DIARY-CAL-TC-021 | Deficit/surplus status is not conveyed by color alone | On Diary page | 1. Inspect whether deficit/surplus is indicated only by a color (e.g. red/green text) without an accompanying text label | Status is conveyed via text ("deficit"/"surplus"), with color as a secondary reinforcement only — meets WCAG 1.4.1 (use of color) | | | P2 |
| DIARY-CAL-TC-022 | Color contrast of all Calorie Ledger text | On Diary page | 1. Check contrast ratio of Meals/Resting/Active/Balance values and labels | Meets WCAG AA (4.5:1 text) | | | P3 |

## D. Food Log card

**Scope:** the meal list itself (as distinct from Section C's arithmetic/messaging, which reads the
same underlying meal data). **Known constraint, same as Section C:** the "Log meals"/add button here
opens the identical mobile hand-off modal — there is no web-based way to create a meal, so anything
requiring a *newly*-logged meal is BLOCKED. Where a day with pre-existing, already-synced meal data is
available (from a prior test session or app-side logging), passive verification of list rendering,
ordering, and cross-card arithmetic IS possible and is written as its own case rather than lumped into
"blocked."

### D.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-FOOD-TC-001 | Empty state renders correctly when no meals are logged | On Diary page, a day with 0 meals logged | 1. Load that day 2. Read the Food Log card | Shows "No food logged for this day" (or equivalent) + an add-meal button — no error, no blank card | | | P2 |
| DIARY-FOOD-TC-002 | [Regression guard, Bug #1 pattern] No meal from a different day appears misattributed to the viewed day | On Diary page, a day confirmed to have 0 meals logged this day, but with a meal genuinely logged on an adjacent day | 1. Load that day 2. Inspect the Food Log list AND the `today/overview` API's activity feed for any meal entry | Zero meal entries appear for this day — specifically confirms Food Log does NOT share Water's known cross-day misattribution bug (#1); if it does reproduce, escalate as a new, separately-filed P1 (same root-cause family, but a materially different, higher-impact surface — food data, not just water) | | | P1 |
| DIARY-FOOD-TC-003 | If a day with real logged meal(s) exists: list items render accurate name/time/calorie value | A day with ≥1 real logged meal (from any prior session or app-side log) — if none exists in current test data, log this case as untested-for-lack-of-data, not a false pass | 1. Load that day 2. Read each meal row's name, time, and calorie value | Each field matches the real logged data exactly | | | P1 |
| DIARY-FOOD-TC-004 | Sum of Food Log's listed calorie values equals Calorie Ledger's "Meals" figure | Same day as TC-003 | 1. Sum the calorie value of every row in the Food Log list 2. Compare to the Calorie Ledger card's "Meals" value (Section C) | The two independently-rendered figures match exactly — a genuine cross-card data-integrity check, since both are presumably sourced from the same underlying meal data but rendered by different card components | | | P1 |
| DIARY-FOOD-TC-005 | "Log meals" button opens the same shared hand-off modal as the Calorie Ledger card's equivalent control | On Diary page | 1. Click Food Log's add/"Log meals" button 2. Compare modal content/behavior to Calorie Ledger's (Section C, TC-012) | Identical modal — confirms this is genuinely the same shared control/component, not a divergent second implementation with its own potential bugs | | | P3 |
| DIARY-FOOD-TC-006 | Food Log state correctly changes when navigating dates | On Diary page, navigate across days with differing meal data (some empty, some populated if available) | 1. Navigate through 2–3 days 2. Confirm Food Log updates for each | Card correctly shows each day's own real state (empty or populated with that day's meals) — cross-check against Section K's multi-date sweep | | | P1 |
| DIARY-FOOD-TC-007 | [BLOCKED — app-only] A newly-logged meal appears in the list immediately and correctly | On Diary page, Today | 1. Attempt to log a meal directly from web | Cannot be executed — no web form exists. **Hand off to Android QA:** log a meal on the app, then reload this web page (without web-side changes) and confirm it appears in the Food Log list with correct name/time/calorie value, in the correct chronological position relative to any other same-day meals | | | P1 — untestable from web |
| DIARY-FOOD-TC-008 | [BLOCKED, conditional] Multiple same-day meals render in the correct order | Requires a day with 2+ real logged meals — if such a day exists, this becomes directly testable (not blocked); if not, hand off to Android QA alongside TC-007 | 1. If available: load that day, read the order of meal rows | Rows are ordered logically (chronological by log time, matching the pattern already confirmed on the Activities card) — verify directly if data exists, otherwise document as pending real multi-meal test data | | | P2 |
| DIARY-FOOD-TC-009 | Clicking a logged meal row — edit/delete affordance check | Same day as TC-003 (a day with a real logged meal) | 1. Click a meal row | **Verify, don't assume:** does anything happen (edit modal, delete option) or is it inert, matching the known no-affordance gap already confirmed on the Activities card? Log actual behavior — if inert, this is a consistent, already-known scope gap, not a new bug | | | P2 |

### D.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-FOOD-TC-010 | Empty-state copy and iconography are clear and on-brand | On Diary page, empty day | 1. Read empty-state text and icon | Copy is clear ("No food logged for this day"), icon (if any) is relevant, not a generic/mismatched placeholder graphic | | | P4 |
| DIARY-FOOD-TC-011 | List item layout consistency with the Activities card's list pattern | Diary page, a day with logged meals (if available) and a day with logged activities | 1. Compare row layout (icon/avatar, name, time, value alignment) between Food Log and Activities cards | Consistent list-row design-system treatment across both list-type cards | | | P3 |
| DIARY-FOOD-TC-012 | Add-button icon/label consistency with other cards' add affordances | On Diary page | 1. Compare Food Log's add button styling/label against Sleep's "Add Sleep Data" and Vitals' "Log weight"/"Log mood" buttons | Consistent button style, icon placement, and copy pattern ("Log X" phrasing) across all add affordances on the page | | | P4 |
| DIARY-FOOD-TC-013 | Grammar/copy review | On Diary page | 1. Read all Food Log card text (heading, empty state, button label) | No typos/grammatical errors | | | P4 |

### D.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-FOOD-TC-014 | Add button has an accessible name | On Diary page | 1. Inspect DOM/accessibility tree for the add/"Log meals" button | Has a real accessible name (visible text or `aria-label`), not an unlabeled icon-only control | | | P2 |
| DIARY-FOOD-TC-015 | Meal list is marked up as a real list for assistive tech (if populated data is available) | Same day as TC-003 | 1. Inspect DOM structure of the meal rows | Uses semantic list markup (`<ul>`/`<li>` or an equivalent ARIA `list`/`listitem` role), not bare unstructured `<div>`s | | | P3 |
| DIARY-FOOD-TC-016 | Touch target size of the add button and (if present) meal rows | On Diary page, mobile viewport | 1. Measure tappable areas | Meets ≥44×44px minimum — log only if there's a concrete, visible tap-reliability problem | | | P4 |

## E. Sleep card

**Scope:** Sleep is the one Diary card with a genuine, fully web-testable write path (a real modal
with start/end time pickers — not an app hand-off), and the only card with a confirmed clean prior
result (0 bugs, in-place update, correct persistence). That makes it the best candidate on this page
for the full positive/negative/edge input-validation sweep — everything here is actually executable
from the web, unlike Calorie Ledger/Food Log's blocked meal-logging scenarios.

### E.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SLEEP-TC-001 | Empty state renders correctly | On Diary page, a day with no sleep logged | 1. Load that day 2. Read the Sleep card | Shows "No Data" / "Track your sleep to see insights" + "Add Sleep Data" button | | | P2 |
| DIARY-SLEEP-TC-002 | Logging a valid sleep entry updates the card in place immediately | On Diary page, Today, no sleep logged yet | 1. Click "Add Sleep Data" 2. Enter start 9:00 PM / end 5:00 AM (8h) 3. Save | Card switches from empty state to "8 hrs 0 mins" the moment the modal closes — no manual reload/nav-away required | | | P1 |
| DIARY-SLEEP-TC-003 | Logged duration persists through a hard reload | Following TC-002 | 1. Hard reload the page | "8 hrs 0 mins" still displays correctly | | | P1 |
| DIARY-SLEEP-TC-004 | Overnight-spanning entry computes duration correctly across midnight | On Diary page, Today | 1. Log start 11:00 PM / end 6:00 AM | Displayed duration is exactly 7h 0m; entry is attributed to a single, sensible day (verify and document which — the start day or the end day — since this directly determines correct date-attribution, echoing the theme of Bug #1) | | | P1 |
| DIARY-SLEEP-TC-005 | Editing an existing entry replaces rather than duplicates it | On Diary page, a day with an existing sleep entry (from TC-002) | 1. Note current duration 2. Open edit affordance, change to a distinct new duration (e.g. 6h 30m) 3. Save | Card shows exactly the new duration — old value is replaced, not shown as a second entry; persists through hard reload | | | P1 |
| DIARY-SLEEP-TC-006 | Sleep data stays correct when navigating dates | On Diary page, 2+ days with distinct known sleep durations | 1. Navigate between those days | Each day shows its own correct duration (or empty state) — cross-check against Section K's multi-date sweep | | | P1 |
| DIARY-SLEEP-TC-007 | Sleep duration cross-checks against Summary's "Avg Sleep" Trends tile | Diary and Summary both loaded, same week | 1. Note today's logged duration on Diary 2. Check Summary's Avg Sleep trend bar for today | Today's contribution to the average is consistent with the logged duration (bar height / value math checks out — cross-reference Summary's own Trends test coverage) | | | P2 |
| DIARY-SLEEP-TC-008 | Very short duration is accepted and displayed accurately | On Diary page | 1. Log start 6:45 AM / end 7:00 AM (15 min) | Displays "0 hrs 15 mins" (or equivalent) — no rounding up to a full hour, no rejection | | | P2 |
| DIARY-SLEEP-TC-009 | Near-24h duration is accepted and displayed accurately | On Diary page | 1. Log start 12:01 AM / end 12:00 AM the next day (23h 59m) | Displays "23 hrs 59 mins" accurately, no overflow/wraparound to 0 | | | P2 |
| DIARY-SLEEP-TC-010 | Exactly 24h duration (start = end, 24h apart) — behavior check | On Diary page | 1. Attempt start 12:00 AM / end 12:00 AM the next day | **Verify, don't assume:** does the app accept this as "24 hrs 0 mins," silently cap it, or reject it as invalid? Log the actual behavior rather than assuming a specific correct answer, since no spec confirms the intended cap | | | P2 |
| DIARY-SLEEP-TC-011 | Zero-duration entry (start = end, same time) — behavior check | On Diary page | 1. Attempt to set start and end to the identical time | **Verify, don't assume:** does the UI block this with a validation message, or silently accept "0 hrs 0 mins"? Either could be correct by design — log actual behavior; only flag as a bug if it silently accepts 0 with no feedback AND that's inconsistent with how other 0-value edge cases are handled elsewhere (e.g. Weight/Water reject nonsensical zero-equivalent inputs) | | | P3 |
| DIARY-SLEEP-TC-012 | End-time-before-start-time on the same visual day, without an explicit "overnight" toggle | On Diary page | 1. If the picker has no explicit AM/PM-spanning-day indicator, set start 8:00 AM and end 6:00 AM without any overnight flag | **Verify, don't assume:** does the app auto-interpret this as spanning to the next day (22h), reject it, or silently produce a nonsensical/negative duration? This is the highest-risk edge case for a real data-integrity bug — log the exact resulting displayed duration and flag as P1 if it produces an obviously wrong (e.g. negative or nonsensical) value | | | P1 |
| DIARY-SLEEP-TC-013 | Non-round-number duration displays exact minutes, no rounding | On Diary page | 1. Log start 10:23 PM / end 6:00 AM (7h 37m) | Displays "7 hrs 37 mins" exactly — no rounding to the nearest 5/10/15 minutes | | | P2 |
| DIARY-SLEEP-TC-014 | Rapid double-submit does not create a duplicate entry | On Diary page | 1. Fill in a valid sleep entry 2. Double-click "Save" as fast as possible | Only one entry is saved/displayed — no duplicate submission, no error from a race condition | | | P2 |

### E.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SLEEP-TC-015 | Empty-state copy and iconography are clear | On Diary page, empty day | 1. Read empty-state text/icon | Copy is clear and encouraging ("Track your sleep to see insights"), icon is relevant | | | P4 |
| DIARY-SLEEP-TC-016 | Time-picker component is visually/behaviorally consistent with other time pickers in the app | On Diary page, Add Sleep Data modal open | 1. Compare this time-picker's style/interaction to any other time-based input in the app (if one exists) | Consistent design-system component reuse, not a bespoke one-off picker | | | P3 |
| DIARY-SLEEP-TC-017 | Duration display format is consistent with other duration displays on the page | On Diary page | 1. Compare Sleep's "8 hrs 0 mins" format against Activities card's duration format (e.g. "30 min") | Consistent phrasing/units convention across the page — flag as a P4 polish item if formats diverge (e.g. "hrs/mins" vs "min") without a clear reason | | | P4 |
| DIARY-SLEEP-TC-018 | Modal heading and button labels are grammatically correct and state-appropriate | On Diary page | 1. Open the modal in both "Add" and "Edit" states | Labels correctly say "Add Sleep Data" vs. an edit-appropriate label/button text depending on state; no typos | | | P4 |

### E.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-SLEEP-TC-019 | "Add Sleep Data" button has an accessible name and is keyboard-reachable | On Diary page | 1. Tab to the button 2. Activate with Enter/Space | Reachable and activatable via keyboard; has a real accessible name | | | P2 |
| DIARY-SLEEP-TC-020 | Modal correctly traps focus (positive contrast check vs. the known-broken hand-off modals) | On Diary page, modal open | 1. Tab repeatedly while the modal is open | Focus stays within the modal's interactive elements and does not leak to background page content — worth confirming explicitly since this is a REAL modal, unlike the app hand-off modals elsewhere that are known to lack a focus trap | | | P2 |
| DIARY-SLEEP-TC-021 | Time-picker inputs are operable via keyboard alone | On Diary page, modal open | 1. Attempt to set start/end time using only keyboard (Tab/arrow keys/typing) | Fully operable without a mouse | | | P2 |
| DIARY-SLEEP-TC-022 | Color contrast of Sleep card text | On Diary page | 1. Check contrast ratio of duration text and labels | Meets WCAG AA (4.5:1 text) | | | P3 |
| DIARY-SLEEP-TC-023 | Touch target size of "Add Sleep Data" button | On Diary page, mobile viewport | 1. Measure tappable area | Meets ≥44×44px minimum — log only if there's a concrete tap-reliability issue | | | P4 |

## F. Intake card (Water/Calories)

**Scope:** the card with the confirmed P1 defect (Bug #1) — Water misattributes a prior day's log to
Today, corrupting three separate surfaces (the aggregate API total, today's activity feed, and the
Log Water modal's own pre-fill) with three mutually-inconsistent wrong numbers. It also reproduces a
known unit-mismatch issue (Bug #34, quick-add area). This section gives Water the deepest regression
treatment of any card on this page — each corrupted surface gets its own isolated assertion so a
partial fix doesn't slip through as "fixed." "Log water" is a real web modal (like Sleep's), so
positive/negative/edge input testing is fully executable here, unlike the app-only meal-logging cards.

### F.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-INTAKE-TC-001 | [Regression — Bug #1 core] Water shows zero on a day with genuinely no water logged | A day confirmed to have zero water ever logged (e.g. an early test day) | 1. Load that day 2. Read the Water row | Shows "0 / <goal>" — no non-zero carryover | | | P1 — baseline (this specific case has passed historically; regressions here would be a new, distinct issue) |
| DIARY-INTAKE-TC-002 | [Regression — Bug #1 signature] Today's Water value does not equal a prior day's value when nothing was logged today | Today has zero water logged; an adjacent day has a genuine non-zero water log | 1. Load Today 2. Compare Water's displayed value to the adjacent day's value | Today's value must NOT equal the adjacent day's value — an exact match is the specific signature of Bug #1 | | | P1 — currently known FAILING |
| DIARY-INTAKE-TC-003 | [Regression — Bug #1, API level] Raw `today/overview` response for Today is not contaminated by another day's water data | Same as TC-002 | 1. Inspect the raw API response body for Today's request 2. Read the nutrition data's Water value/rawValue | Value should be 0 (or reflect a genuine same-day log only) — confirms whether the fix is server-side (API-level) or was ever only a display-layer issue | | | P1 — currently known FAILING (confirmed via API in prior session) |
| DIARY-INTAKE-TC-004 | [Regression — Bug #1, activity feed] Today's activity feed contains no water entry timestamped from a different day | Same as TC-002 | 1. Inspect the `activitiesData` array in the API response for Today | No "Water" entry appears in Today's feed unless a water log genuinely occurred today; if one appears, its timestamp's date must equal today | | | P1 — currently known FAILING (a "Water (11:55 AM)" ghost entry was previously found) |
| DIARY-INTAKE-TC-005 | [Regression — Bug #1, modal pre-fill] Log Water modal's own pre-fill reconciles with the card's displayed value | Same as TC-002 | 1. Open "Log water" modal 2. Read its pre-filled glass count/ml value 3. Compare to the Water row's displayed value and to the raw API value (TC-003) | All three (card row, modal pre-fill, API value) should agree — even a "consistently wrong" state is better than three DIFFERENT wrong numbers, which is what was previously found (card: 25.36/750ml-equivalent, modal: 5000ml/20 glasses) | | | P1 — currently known FAILING (a third, unreconciled value) |
| DIARY-INTAKE-TC-006 | [Regression — Bug #34 lineage, unit mismatch] Water row's numerator and unit label are internally consistent | Any day with a non-zero Water value | 1. Read the exact displayed string (e.g. "25.36/2.5 L") | The numerator's unit should match the stated unit label — a fl-oz-derived number displayed against an "L" label (without conversion) is an internal inconsistency distinct from the date-attribution bug; verify whether this still reproduces | | | P1 |
| DIARY-INTAKE-TC-007 | [Self-seeded, primary automatable case] Logging an exact known water amount reflects correctly end-to-end | On Diary page, Today | 1. Open "Log water" 2. Use the glass stepper (not the pre-fill) to set exactly 3 glasses 3. Save 4. Inspect card row AND raw API response | Card shows the correct resulting total (old genuine amount + 3 glasses' worth, in a consistent unit); API's water entry is timestamped today; no third divergent number appears anywhere | | | P1 |
| DIARY-INTAKE-TC-008 | Isolation check — logging on Today does not retroactively alter a prior day's figure | Following TC-007 | 1. Navigate to the adjacent day used in TC-002 2. Re-check its Water value | Prior day's value is unchanged by today's new log — confirms the contamination (if any) isn't bidirectional | | | P1 |
| DIARY-INTAKE-TC-009 | Glasses stepper increments/decrements by exactly one glass per click | Log Water modal open | 1. Click "Add a glass" 3 times, noting the count after each click 2. Click "Remove a glass" once | Count increases by exactly 1 per click (not 0, 2, or skipping); decreases by exactly 1 | | | P2 |
| DIARY-INTAKE-TC-010 | Glass-to-volume arithmetic is internally consistent | Log Water modal open | 1. Note the ml/L equivalent shown for N glasses 2. Add one more glass, note the new ml/L value | The per-glass ml increment is constant and consistent with the stated conversion (e.g. always +250ml per glass, or whatever the app's real constant is) | | | P2 |
| DIARY-INTAKE-TC-011 | ["X of Y glasses" / "Daily goal reached" logic sanity check] | Log Water modal open, especially in a state showing a high glass count | 1. Read the exact "X of Y glasses" string and any "Daily goal reached" messaging | X should never exceed Y while still being framed as "of Y" (e.g. "20 of 8 glasses" is internally nonsensical — either the cap should prevent exceeding 8, or the copy should reflect an overage differently, e.g. "8+ / 8 glasses"); log the previously-seen "20 of 8" string as a concrete anomaly to re-verify | | | P2 |
| DIARY-INTAKE-TC-012 | Glasses stepper cannot go negative | Log Water modal open, count at 0 | 1. Click "Remove a glass" when count is already 0 | Button is disabled or no-ops — count never goes below 0, no error | | | P2 |
| DIARY-INTAKE-TC-013 | Glasses stepper has a sensible upper bound | Log Water modal open | 1. Click "Add a glass" repeatedly well past the daily goal (e.g. 25+ times) | Either the stepper caps at a sensible maximum, or continues incrementing with correct arithmetic and no overflow/layout break — log the actual cap behavior rather than assuming one | | | P3 |
| DIARY-INTAKE-TC-014 | Daily water goal VALUE consistency across surfaces | Diary Intake card and Quick Add's Log Water flow both checked | 1. Read Diary's stated daily goal (e.g. "2.5 L") 2. Read Quick Add's equivalent goal (per `dashboard/quick-add/bug-logs/bug-log.md` Bug #34, expected "2 L") | The two surfaces should state the SAME daily goal for the same metric — if they genuinely differ (2.5 L vs 2 L), this is a real, separately-reportable data-integrity inconsistency, not just a cosmetic issue | | | P2 |
| DIARY-INTAKE-TC-015 | Calories row shows "0kcal" accurately when 0 meals are logged | A day with 0 meals logged | 1. Read the Calories row | Shows "0kcal" explicitly | | | P2 |
| DIARY-INTAKE-TC-016 | Calories row cross-checks against Calorie Ledger's Meals figure and Food Log's summed values | Same day, all three cards visible/checked | 1. Compare Intake's Calories value, Calorie Ledger's "Meals" value (Section C), and Food Log's summed line items (Section D) | All three agree exactly — a three-way cross-card consistency check on what should be the same underlying number | | | P1 |
| DIARY-INTAKE-TC-017 | Water/Calories correctly reflect each day's own data when navigating (aside from the known Water exception) | 3 consecutive days with distinct known data | 1. Navigate through each day 2. Read Water and Calories | Calories correctly updates per day in all cases; Water correctly updates EXCEPT for the specific known Bug #1 scenario already covered above — confirms the rest of this card's date-nav is sound | | | P1 |
| DIARY-INTAKE-TC-018 | Water logged near midnight attributes to the correct calendar day | On Diary page, ability to log at a time near midnight (or Note/Doubt if not practically reproducible in a manual/automated test window) | 1. Log a glass of water as close to 11:59 PM as feasible 2. Check which day it's attributed to | Attributed to the day it was actually logged on, not the next day — if not practically testable at the exact boundary, log as untested with a note rather than assuming pass | | | P3 |

### F.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-INTAKE-TC-019 | Water row's "X/Y unit" format is clear and unambiguous to a real user | On Diary page | 1. Read the Water row as a first-time user would | Format clearly communicates "current intake / daily goal" in one consistent unit — flag as a judgment call if mixed units (per TC-006) make it confusing rather than just factually wrong | | | P3 |
| DIARY-INTAKE-TC-020 | Log Water modal's stepper UI is consistent with other input components in the app | Log Water modal open | 1. Compare stepper style/interaction to Sleep's time-picker or other numeric inputs | Consistent design-system component treatment | | | P4 |
| DIARY-INTAKE-TC-021 | Grammar/copy review of Intake card and Log Water modal text | On Diary page, modal open | 1. Read all labels, including "Daily goal reached" | No typos/grammatical errors | | | P4 |
| DIARY-INTAKE-TC-022 | "Daily goal reached" messaging tone and correctness | Log Water modal open, at/near goal | 1. Read the messaging when goal is reached (or apparently exceeded, per TC-011) | **Judgment call:** messaging is clear and positive in tone; flag separately if it displays while the underlying count is logically inconsistent (per TC-011) — that's a functional bug, not a copy issue | | | P4 |
| DIARY-INTAKE-TC-023 | Card spacing/typography consistent with other Diary cards | On Diary page | 1. Compare heading/padding/row style to Snapshot, Calorie Ledger | Consistent design-system treatment | | | P4 |

### F.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-INTAKE-TC-024 | "Log water" button has an accessible name and is keyboard-operable | On Diary page | 1. Tab to the button 2. Activate with Enter/Space | Reachable and activatable via keyboard; has a real accessible name | | | P2 |
| DIARY-INTAKE-TC-025 | Glass stepper +/- controls have accessible labels and are keyboard-operable | Log Water modal open | 1. Inspect DOM for "Add a glass"/"Remove a glass" accessible names 2. Operate both via keyboard | Both controls have clear accessible names (not bare "+"/"−" icons with no label) and work via keyboard | | | P2 |
| DIARY-INTAKE-TC-026 | Log Water modal correctly traps focus | Modal open | 1. Tab repeatedly while the modal is open | Focus stays within the modal (this is a real modal, like Sleep's — verify it also traps focus correctly, independent of the data bug) | | | P2 |
| DIARY-INTAKE-TC-027 | Color contrast of Intake card and modal text | On Diary page, modal open | 1. Check contrast ratio of all text/labels | Meets WCAG AA (4.5:1 text) | | | P3 |
| DIARY-INTAKE-TC-028 | Touch target size of stepper +/- buttons | Modal open, mobile viewport | 1. Measure tappable area of both stepper buttons | Meets ≥44×44px minimum — log only if there's a concrete tap-reliability issue | | | P4 |

## G. Distance card

**Scope:** Moved/Jog-Run/Cycling distance rows. **Open question from the prior exploratory pass:**
this card showed "—" (empty) on every day tested so far, including a day with 7 logged activities
(Hiking, Swimming, Yoga — 14 July). That was provisionally treated as "a separate device/GPS-based
data source, not a bug," but none of those 3 activity types actually match this card's own row
categories (Moved/Jog-Run/Cycling) — so the empty result may simply mean **no matching-category
activity has ever been tried**, not that the feature is confirmed disconnected from manually-logged
activities. `TC-001`–`003` below directly test the untried hypothesis: log an activity whose type
genuinely matches a row (Running/Jog, Cycling, Walking) and see if it populates. This is the one open
item from Section G's prior pass that still needs real evidence either way.

### G.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-DIST-TC-001 | [Untested hypothesis] Logging a "Running"/"Jog" activity with distance populates the Jog-Run row | On Diary page, Today | 1. Log a "Running" (or app's exact "Jog-Run"-category label) activity via Quick Add, with an explicit distance value (e.g. 5 km) 2. Return to Diary, check the Distance card's Jog-Run row | **This is the real open question:** does the row populate with the logged distance (proving the card DOES read manually-logged activities of the matching type, and the prior "—" results were simply because no matching-type activity had been tried), or does it stay "—" (proving it's genuinely a separate, device-only data source as previously hypothesized)? Either outcome is a valid, useful result — log exactly which one occurs | | | P1 |
| DIARY-DIST-TC-002 | [Untested hypothesis] Logging a "Cycling" activity with distance populates the Cycling row | On Diary page, Today | 1. Log a "Cycling" activity with an explicit distance (e.g. 10 km) 2. Check the Cycling row | Same open question as TC-001, applied to the Cycling category | | | P1 |
| DIARY-DIST-TC-003 | [Untested hypothesis] Logging a "Walking" activity populates the Moved row | On Diary page, Today | 1. Log a "Walking" (or the app's exact "Moved"-category label) activity 2. Check the Moved row | Same open question as TC-001, applied to the Moved category | | | P1 |
| DIARY-DIST-TC-004 | Non-matching-category activities correctly do NOT populate any Distance row | A day with only non-matching activities logged (e.g. 14 July: Hiking/Swimming/Yoga) | 1. Load that day 2. Check all 3 Distance rows | All show "—" — and per TC-001–003's findings, this is now explained (no Jog-Run/Cycling/Moved-type activity was logged that day), not a bug | | | P2 |
| DIARY-DIST-TC-005 | If TC-001–003 populate a row: the displayed distance matches what was actually logged | Following whichever of TC-001–003 succeeded | 1. Compare the row's displayed distance to the value entered when logging | Exact match, correct unit conversion if units differ between entry and display | | | P1 |
| DIARY-DIST-TC-006 | If TC-001–003 populate a row: the data stays correctly scoped to that specific day | Following TC-005 | 1. Navigate to an adjacent day with no such activity logged | That day's Distance rows remain "—" — the newly-logged distance does not leak into other days | | | P1 |
| DIARY-DIST-TC-007 | Unit label ("mile") is static text, no toggle | On Diary page | 1. Attempt to click/interact with the "mile" unit label | Plain text, no click handler, no unit-switch UI — confirms this is a scope limitation (no imperial/metric toggle), not a broken feature | | | P3 |
| DIARY-DIST-TC-008 | [Product question, not directly testable] Device/GPS-based passive movement data | Requires a real device-linked account | N/A — cannot be tested with the current demo/web-only account | Document as untested-for-lack-of-data; recommend testing with a real wearable-linked account if one becomes available, rather than treating the current empty results as a confirmed pass or fail | | | P3 — untestable with current test account |

### G.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-DIST-TC-009 | Unit consistency between Distance card ("mile"/"km") and Activities card (which shows raw meters, e.g. "5,000m") | Diary page, a day with a distance-bearing activity logged | 1. Compare the unit convention shown on Activities' row (meters) vs. Distance card's label (miles/km) | **Note/Doubt, not an automatic bug:** two cards on the same page use different distance units/scales for what may be related data — flag for a design decision on whether these should share one unit convention, rather than assuming either is wrong | | | P3 |
| DIARY-DIST-TC-010 | Empty-state symbol ("—") consistency with other cards' empty-state conventions | On Diary page, empty Distance rows vs. Sleep's "No Data" / Food Log's "No food logged for this day" | 1. Compare how "no data" is communicated across cards | **Design-system inconsistency worth a judgment call:** other cards use explicit worded empty states, Distance uses a bare em-dash per row — flag as a P3 consistency finding, not a functional bug, since a dash is a defensible minimalist choice for a compact 3-row card | | | P3 |
| DIARY-DIST-TC-011 | Row label clarity ("Moved" / "Jog-Run" / "Cycling") | On Diary page | 1. Read the three row labels as a first-time user | **Judgment call:** are these category names self-explanatory (e.g. does "Moved" clearly mean walking-distance to an average user)? Note as an opinion, not a bug, unless genuinely confusing | | | P4 |
| DIARY-DIST-TC-012 | Grammar/copy review | On Diary page | 1. Read all Distance card text | No typos/grammatical errors | | | P4 |
| DIARY-DIST-TC-013 | Card spacing/typography consistent with other Diary cards | On Diary page | 1. Compare heading/row style to Snapshot, Intake | Consistent design-system treatment | | | P4 |

### G.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-DIST-TC-014 | "—" empty-state symbol has a meaningful accessible equivalent | On Diary page, empty row | 1. Inspect DOM/accessibility tree for an empty row | Screen readers should announce something meaningful ("No data" or similar), not silently skip the row or read a bare dash character with no context | | | P2 |
| DIARY-DIST-TC-015 | Card is not an unintended keyboard/tab stop if purely informational | On Diary page | 1. Tab through the page, confirm whether the Distance card itself receives focus | If the card has no interactive function, it should not create a spurious tab stop — confirm no redundant-focus issue like the ones found on Snapshot | | | P3 |
| DIARY-DIST-TC-016 | Color contrast of Distance card text/labels | On Diary page | 1. Check contrast ratio of row labels and unit text | Meets WCAG AA (4.5:1 text) | | | P3 |

## H. Activities card

**Scope:** the "N logged" counter, individual activity rows (name/time/duration/distance), the
"View all" affordance, and edit/delete interaction. Logging an activity is a real web modal (via Quick
Add's Log Activity flow, already covered functionally in `dashboard/quick-add/test-cases/log-activity.md`)
— this section focuses on how the **Diary card itself** reflects that data: in-place updates,
ordering, cross-day scoping, and two already-known gaps (the "View all" button's mislabeled behavior,
and no edit/delete affordance on rows) that get re-verified here as reproductions, not re-filed.

### H.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-ACT-TC-001 | "N logged" counter matches the actual row count, whatever N is | On Diary page, any day | 1. Read the counter (or empty-state copy if 0) 2. Count actual rows | Counter exactly equals row count; correct empty-state copy with zero phantom rows if 0 | | | P1 |
| DIARY-ACT-TC-002 | Logging a new activity adds it to the list immediately, without a hard reload | On Diary page, Today | 1. Note current counter/rows 2. Log a new activity via Quick Add (e.g. "Running", 20 min, 3 km) 3. Return to/stay on Diary without reloading | **Verify, don't assume:** does the row appear immediately (matching Sleep's good in-place-update pattern) or does it require a nav-away-and-back/hard reload (matching Summary's known Bug #2 pattern)? Log actual behavior | | | P1 |
| DIARY-ACT-TC-003 | New activity persists correctly through a hard reload | Following TC-002 | 1. Hard reload 2. Re-check the list | Row still present with correct name/time/duration/distance | | | P1 |
| DIARY-ACT-TC-004 | Multiple same-day activities render in correct chronological order | On Diary page, Today | 1. Log two activities with deliberately controlled, known times (e.g. 9:00 AM Yoga, then 2:00 PM Running) 2. Read row order | Rows appear in chronological order (earliest first, matching the previously-observed pattern) — this uses controlled test data instead of relying on historic logs, so it's fully deterministic | | | P1 |
| DIARY-ACT-TC-005 | "View all"/expand icon — reproduction of known mislabeled-behavior bug | On Diary page, a day with 1+ activities | 1. Click the small icon button next to "N logged" | **Reproduction check:** confirms whether it still opens the "add new activity" Log Activity picker instead of showing the full list (known bug, quick-add area's Bug #9) — not re-filed as new if reproduced | | | P2 |
| DIARY-ACT-TC-006 | Clicking a logged activity row — no edit/delete affordance (known gap) | On Diary page, a day with 1+ activities | 1. Click a logged activity row (e.g. "Hiking") | **Reproduction check:** confirms whether nothing happens (known scope gap, not a new defect) or whether an edit/delete option has since been added (would be a positive product change worth noting) | | | P2 |
| DIARY-ACT-TC-007 | Activities list/counter stays correctly scoped per day when navigating | 3 consecutive days with distinct known activity counts | 1. Navigate through each day 2. Read counter and rows | Each day shows its own correct data — cross-check against Section K's multi-date sweep | | | P1 |
| DIARY-ACT-TC-008 | Distance value in a row matches what was logged (cross-check with Section G) | Following TC-002 (an activity logged with an explicit distance) | 1. Compare the row's displayed distance to the value entered | Exact match; also cross-reference whether this activity's type causes Section G's Distance card to populate (see DIARY-DIST-TC-001–003) | | | P1 |
| DIARY-ACT-TC-009 | Different activity types render with correct name/icon | On Diary page | 1. Log 2–3 distinct activity types (e.g. Running, Cycling, Yoga) 2. Check each row's icon and label | Each type shows its own correct, distinct icon — no generic/missing/wrong icon | | | P2 |
| DIARY-ACT-TC-010 | Very short duration (e.g. 1 minute) is accepted and displayed accurately | On Diary page | 1. Log an activity with 1 min duration | Displays "1 min" (or equivalent) exactly, not rounded up or rejected | | | P2 |
| DIARY-ACT-TC-011 | Very long duration (e.g. 5 hours) is accepted and displayed without overflow | On Diary page | 1. Log an activity with a 300 min duration | Displays correctly (e.g. "5 hrs 0 mins" or "300 min", whichever the app's convention is), no layout break | | | P3 |
| DIARY-ACT-TC-012 | Activity logged near midnight attributes to the correct calendar day | On Diary page, near midnight (or Note/Doubt if not practically reproducible) | 1. Log an activity as close to 11:59 PM as feasible 2. Check which day it's attributed to | Attributed to the day it was actually logged, not the next day — a direct regression-style check against the date-attribution theme raised by Bug #1; log as untested-with-a-note if the exact boundary isn't practically reachable | | | P1 |
| DIARY-ACT-TC-013 | Rapid double-submit does not create a duplicate row | On Diary page | 1. Fill in a valid activity log 2. Double-click Save as fast as possible | Only one row is created, no duplicate | | | P2 |
| DIARY-ACT-TC-014 | Unrealistic distance value — validation/cap behavior | On Diary page, Log Activity form | 1. Attempt to enter an unrealistic distance (e.g. 99,999 km) for an activity | **Verify, don't assume:** does the form validate/cap this, or silently accept it and later break the Activities row or Distance card's layout? Log actual behavior; flag as a bug only if it's silently accepted AND visibly breaks a downstream display | | | P3 |
| DIARY-ACT-TC-015 | Very long custom activity name (if free-text naming exists) does not break row layout | On Diary page, Log Activity form (if a custom-name field exists) | 1. Enter an unusually long activity name, if the field allows free text | Row truncates gracefully (ellipsis or wrap) rather than breaking layout/overlapping other row content | | | P4 |

### H.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-ACT-TC-016 | Row layout consistency (icon, name, time, duration, distance alignment) | On Diary page, 2+ activities logged | 1. Inspect alignment/spacing of row elements across multiple rows | Consistent column alignment across all rows, no jitter/misalignment between rows of different content length | | | P4 |
| DIARY-ACT-TC-017 | "View all" icon visually implies an action that doesn't match its actual behavior | On Diary page | 1. Inspect the icon used for "View all" | **Judgment call, related to but distinct from TC-005's functional bug:** if the icon looks like an "expand/view list" affordance (e.g. a chevron or list icon) but actually opens an add-activity flow, that's a copy/icon-vs-behavior mismatch worth flagging as its own UX finding, separate from the underlying navigation bug | | | P3 |
| DIARY-ACT-TC-018 | List row style consistency with Food Log's list pattern | Diary page, both cards with data (if available) | 1. Compare row layout/typography between Activities and Food Log cards | Consistent list-row design-system treatment across both list-type cards | | | P4 |
| DIARY-ACT-TC-019 | "N logged" counter copy clarity | On Diary page | 1. Read the exact counter phrasing | Clear, grammatically correct, consistent with similar counters elsewhere in the app | | | P4 |
| DIARY-ACT-TC-020 | Grammar/copy review of all Activities card text | On Diary page | 1. Read all labels/empty-state text | No typos/grammatical errors | | | P4 |

### H.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-ACT-TC-021 | "View all" button has an accurate accessible name given its actual behavior | On Diary page | 1. Inspect the button's `aria-label`/accessible name | **Related to TC-005/017:** if labeled "View all" but it actually opens an add-activity flow, the accessible name itself is misleading to screen-reader users too, not just visually — worth noting as an accessibility-flavored instance of the same underlying bug | | | P2 |
| DIARY-ACT-TC-022 | Activity list uses semantic list markup | On Diary page, 1+ activities | 1. Inspect DOM structure of the rows | Uses `<ul>`/`<li>` or equivalent ARIA list/listitem roles, not bare unstructured `<div>`s | | | P3 |
| DIARY-ACT-TC-023 | Rows that are visually clickable but functionally inert are not falsely implied as interactive | On Diary page, a logged activity row | 1. Inspect cursor style / hover state / DOM role of a row (given TC-006 confirms no edit/delete happens) | If the row shows `cursor: pointer` or a hover highlight but does nothing on click, that's a real UX affordance mismatch (implies interactivity that isn't there) — distinct from simply "no feature exists yet" | | | P3 |
| DIARY-ACT-TC-024 | Color contrast of row text (name, time, duration, distance) | On Diary page | 1. Check contrast ratio of all row text | Meets WCAG AA (4.5:1 text) | | | P3 |
| DIARY-ACT-TC-025 | Touch target size of "View all" icon button | On Diary page, mobile viewport | 1. Measure tappable area | Meets ≥44×44px minimum — log only if there's a concrete tap-reliability issue | | | P4 |

## I. Vitals card (Mood/Heart Rate/Weight)

**Scope:** Mood and Weight both have genuine web modals (like Sleep/Water); Heart Rate is a confirmed
app-only hand-off. That makes Mood and Weight fully eligible for positive/negative/edge input testing
— Weight especially, since it's a free-form numeric health value where bad input handling (negative,
zero, absurd values) is a real data-integrity/safety concern, not just a cosmetic one.

### I.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-VIT-TC-001 | Mood empty state renders correctly | On Diary page, a day with no mood logged | 1. Load that day 2. Read Mood row | Shows "--" and a "Log mood" button | | | P2 |
| DIARY-VIT-TC-002 | Logging mood updates in place immediately | On Diary page, Today, no mood logged | 1. Click "Log mood" 2. Select an option (e.g. "Good") 3. Save | Row updates immediately to "Good"; button switches to "Edit mood" | | | P1 |
| DIARY-VIT-TC-003 | Editing mood correctly pre-selects the current value | Following TC-002 | 1. Click "Edit mood" 2. Inspect the modal | The previously-selected mood ("Good") is shown pressed/selected | | | P1 |
| DIARY-VIT-TC-004 | Editing mood replaces rather than duplicates the value | Following TC-003 | 1. Select a different mood (e.g. "Not Good") 2. Save | Row shows only the new mood; no duplicate/second entry; persists through hard reload | | | P1 |
| DIARY-VIT-TC-005 | Mood correctly reflects each day's own logged value when navigating | 2+ days with distinct known mood values | 1. Navigate between them | Each day shows its own correct mood (or "--" if none logged that day) | | | P1 |
| DIARY-VIT-TC-006 | Rapid double-submit of mood does not create an inconsistent state | On Diary page | 1. Select a mood, double-click Save quickly | Only the intended mood is saved, no error/flicker between two values | | | P2 |
| DIARY-VIT-TC-007 | Weight empty state (never logged) renders correctly | On Diary page, a day/account state with weight never logged | 1. Read Weight row | Shows "--" and "Log weight" button, with NO "Same as last log" hint (since there's no prior log) | | | P1 |
| DIARY-VIT-TC-008 | Logging weight for the first time updates in place and persists | On Diary page, Today | 1. Click "Log weight" 2. Enter a distinct value (e.g. 70.0 kg) 3. Save 4. Hard reload | Row shows "70.0 kg" immediately, and still shows it after reload; button switches to "Edit weight" | | | P1 |
| DIARY-VIT-TC-009 | Editing weight correctly pre-fills the last logged value with "Same as last log" hint | Following TC-008 | 1. Click "Edit weight" 2. Inspect the modal | Pre-fills "70.0 kg" with a "Same as last log" indicator | | | P1 |
| DIARY-VIT-TC-010 | Editing weight replaces rather than duplicates the value | Following TC-009 | 1. Enter a new distinct value (e.g. 71.5 kg) 2. Save | Row shows only 71.5 kg; old value is replaced, not duplicated; persists through hard reload | | | P1 |
| DIARY-VIT-TC-011 | Weight matches Summary page's Vitals card exactly | Diary and Summary both loaded, same underlying data | 1. Compare Weight values on both pages | Identical value — confirms no cross-page discrepancy (this card has previously been clean here, unlike Water's Bug #1) | | | P1 |
| DIARY-VIT-TC-012 | kg↔lbs conversion is mathematically correct | On Diary page, a known weight value | 1. Read the kg value and its lbs-equivalent (if both are shown, e.g. "74.6 kg (164.4 lbs)") | Conversion is mathematically accurate (1 kg = 2.20462 lbs) — re-verify the previously-confirmed 74.6 kg = 164.4 lbs pairing still holds | | | P2 |
| DIARY-VIT-TC-013 | Decimal precision is preserved, not rounded/truncated unexpectedly | On Diary page | 1. Log a precise decimal value (e.g. 74.65 kg) | Displays exactly "74.65 kg" (or the app's defined precision, e.g. rounds consistently to 1 decimal) — verify actual rounding behavior rather than assuming full precision is kept | | | P2 |
| DIARY-VIT-TC-014 | Weight input rejects unrealistic/unsafe values | On Diary page, Log Weight modal | 1. Attempt to enter 0 kg 2. Attempt a negative value (if the input allows typing one) 3. Attempt an absurdly high value (e.g. 500 kg) | **Verify, don't assume — flag as P1 if any of these are silently accepted:** a health app accepting 0/negative/absurd weight values without validation is a real data-integrity and potential safety-messaging concern (e.g. downstream calorie/BMI calculations using a bad value), not merely a cosmetic issue | | | P1 |
| DIARY-VIT-TC-015 | Logging weight while viewing a past day attributes it to that day, not Today | On Diary page, navigated to a past day (e.g. 3 days ago) | 1. From that past day's view, click "Log weight" (if the button is available/active while viewing a non-Today date) 2. Enter a distinct test value 3. Save 4. Navigate to Today and re-check | **Verify, don't assume:** does the logged value attribute to the day being viewed, or does it always write to Today regardless of which day is displayed? This is a direct date-attribution check in the spirit of Bug #1 — flag as P1 if it writes to the wrong day | | | P1 |
| DIARY-VIT-TC-016 | Weight display on a day with no NEW log — carries forward last known value or shows "--"? | On Diary page, a day after a weight was logged, where no new weight entry was made that specific day | 1. Load a day after TC-008/010's log, with no new entry made | **Verify, don't assume:** weight is typically a "most recent known value as of this date" metric, not a daily-reset one like Steps — determine and document which behavior the app actually implements, since assuming the wrong model could misread a correct "--" as a bug, or vice versa | | | P2 |
| DIARY-VIT-TC-017 | Heart Rate confirmed app-only for logging | On Diary page | 1. Inspect the Heart Rate row's action | Shows "Log heart rate on the app" — consistent with the known mobile-hand-off pattern | | | P2 |
| DIARY-VIT-TC-018 | Heart Rate value DOES surface on web once logged via the app (read-only cross-platform check) | Requires a Heart Rate value logged via the Android app on a known day | 1. Log a Heart Rate reading on the Android app 2. Load the same day on the web Diary page | **Untested in the prior pass, verify don't assume:** does the value appear on web (read-only), or does "app-only" mean the web never displays it at all, even after syncing? Document actual behavior — this is a genuine open question, not yet confirmed either way | | | P2 — requires cross-platform (Android+Web) coordination |

### I.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-VIT-TC-019 | "--" empty-state symbol consistency across Mood/Heart Rate/Weight rows and other cards | On Diary page | 1. Compare "--" usage here to Distance card's "—" (Section G) and Sleep/Food Log's worded empty states | Note as a P3/P4 design-system consistency observation if symbols/conventions differ without a clear pattern (e.g. why some cards use a dash and others full sentences) | | | P4 |
| DIARY-VIT-TC-020 | "Log X" vs "Edit X" button-label pattern is consistent across Mood/Weight/Sleep | On Diary page, all three in both empty and logged states | 1. Compare button label patterns across all three | Consistent "Log X" → "Edit X" transition pattern for all loggable metrics on the page | | | P3 |
| DIARY-VIT-TC-021 | Mood selector visual quality (icons/emoji, if used) | On Diary page, Log Mood modal open | 1. Inspect mood option icons | Icons render clearly, are not pixelated/mismatched, and clearly differentiate each mood option | | | P4 |
| DIARY-VIT-TC-022 | Weight unit display formatting clarity (primary/secondary unit pairing) | On Diary page | 1. Read the exact Weight row format | Clear which unit is primary vs. converted-secondary; consistent with how weight is shown elsewhere (e.g. Summary's Vitals card) | | | P3 |
| DIARY-VIT-TC-023 | Grammar/copy review of all Vitals card text | On Diary page, all 3 rows in various states | 1. Read all labels/button text/modal copy | No typos/grammatical errors | | | P4 |

### I.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-VIT-TC-024 | "Log mood"/"Log weight" buttons have accessible names and are keyboard-operable | On Diary page | 1. Tab to each button 2. Activate with Enter/Space | Both reachable and operable via keyboard, with real accessible names | | | P2 |
| DIARY-VIT-TC-025 | Mood selector options each have accessible labels, not just visual icons | Log Mood modal open | 1. Inspect DOM/accessibility tree for each mood option | Each has a text accessible name (e.g. `aria-label="Good"`), not solely conveyed by an icon image with no label | | | P2 |
| DIARY-VIT-TC-026 | Weight input uses an appropriate input type/keyboard on mobile | Log Weight modal open, mobile viewport | 1. Focus the weight input field on a mobile device/emulator | A numeric keyboard is presented (`inputmode="decimal"` or `type="number"`), not a full alphanumeric keyboard | | | P3 |
| DIARY-VIT-TC-027 | Mood/Weight modals correctly trap focus | Either modal open | 1. Tab repeatedly while open | Focus stays within the modal (these are real modals — verify both, like Sleep's) | | | P2 |
| DIARY-VIT-TC-028 | Color contrast of Vitals card text | On Diary page | 1. Check contrast ratio of all row text/labels | Meets WCAG AA (4.5:1 text) | | | P3 |

## J. Footer / global chrome

**Scope:** the shared app-shell chrome that surrounds the Diary page content — global header (logo,
top icons, Vantage Points widget, profile menu, nav tabs) and the page footer (QR code, tagline, help/
back-to-top buttons, footer links, copyright, "Chat with us" widget). This chrome is the same
component used on Summary, where it's still largely untested (`dashboard/summary/SCOPE.md` Section H
is entirely ⬜ Pending) — so this section is effectively the **first real pass on this chrome**, done
from the Diary page specifically because Diary's taller, denser card layout is more likely to surface
overlap/z-index issues with the floating Chat widget than Summary's page does. Findings here should be
cross-checked against Summary rather than re-tested from scratch there, unless something looks
page-specific.

### J.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-CHROME-TC-001 | Logo navigates correctly from Diary | On Diary page | 1. Click the logo/"VFit US home" | Navigates to `/ng/fit/summary` (same behavior as confirmed from Summary itself) | | | P3 |
| DIARY-CHROME-TC-002 | Nav tab active-state correctly reflects Diary as a Summary sub-page | On Diary page (`/ng/fit/summary/diary`) | 1. Inspect the nav tabs (Summary/Challenges/Programs/Community) | **Genuinely open question:** since Diary's URL nests under `/summary/`, does the "Summary" tab show as active/highlighted while viewing Diary, or does no tab appear active (a real UX gap — the user has no visual confirmation of which top-level section they're in)? Log the actual behavior | | | P2 |
| DIARY-CHROME-TC-003 | Clicking Challenges/Programs/Community from Diary navigates away correctly | On Diary page | 1. Click each of the other 3 nav tabs in turn (separate attempts) | Each correctly navigates to its own section, leaving Diary | | | P3 |
| DIARY-CHROME-TC-004 | Top icons (Home, Redeem, Cart, Notification) function identically from Diary as from Summary | On Diary page | 1. Click each icon in turn | Each behaves identically to its already-confirmed Summary-page behavior — flag any divergence as a new, page-specific finding | | | P3 |
| DIARY-CHROME-TC-005 | Vantage Points widget navigates correctly from Diary | On Diary page | 1. Click the Vantage Points widget | Navigates to `/ng/redeem`, matching Summary's confirmed behavior | | | P4 |
| DIARY-CHROME-TC-006 | Profile menu opens correctly from Diary with the same items as Summary | On Diary page | 1. Open the profile menu | Same menu items/behavior as already confirmed on Summary — confirms shared-component reuse, not a divergent implementation | | | P4 |
| DIARY-CHROME-TC-007 | Email verification banner dismiss state is shared across pages | Email verification banner visible; dismiss it on Summary | 1. Dismiss the banner on Summary 2. Navigate to Diary | Banner remains dismissed on Diary too (shared dismiss state, not per-page) — flag if it reappears, since that would mean dismiss state isn't actually persisted globally | | | P3 |
| DIARY-CHROME-TC-008 | Mobile sign-in QR code encodes a valid, correct deep link | On Diary page (or Summary — shared footer), footer scrolled into view | 1. Inspect the QR code's underlying encoded value (via DOM data attribute or by decoding the image) | Encodes a valid, working deep link to the mobile app (not a placeholder/broken URL) — this is the first real verification of this element anywhere in the suite | | | P2 |
| DIARY-CHROME-TC-009 | "Need Help with Vantage Fit?" button performs its intended action | Footer visible | 1. Click the button | Opens the correct destination (help modal, external help center, or chat trigger — document which it actually is) | | | P3 |
| DIARY-CHROME-TC-010 | "Go back to the Top" scrolls correctly | Footer visible, page scrolled down | 1. Click the button | Page scrolls back to the top of the Diary page | | | P3 |
| DIARY-CHROME-TC-011 | Footer links (FAQ, Contact us, Privacy policy, Terms & conditions, App Store, Google Play) each navigate/open correctly | Footer visible | 1. Click each link in turn (separate attempts; note if any should open in a new tab) | Each opens its correct, working destination — no dead links, no 404s | | | P2 |
| DIARY-CHROME-TC-012 | Copyright text is accurate | Footer visible | 1. Read the copyright line | Correct company name and a plausible/current year (not a stale hardcoded year) | | | P4 |
| DIARY-CHROME-TC-013 | "Chat with us" (Freshchat) widget opens and closes correctly | Footer/floating widget visible | 1. Click to open 2. Click to close | Opens and closes correctly, no stuck/unresponsive state | | | P2 |
| DIARY-CHROME-TC-014 | Chat widget does not overlap Diary's own card content on mobile | On Diary page, mobile viewport, scrolled to various points (especially near the Vitals/Footer cards at the bottom) | 1. Scroll through the full Diary page at 390×844 2. Check for overlap between the floating Chat widget and card content/buttons | No overlap that blocks or obscures interactive card elements — this is a KNOWN systemic pattern already found on Summary (Bugs #24/#28/#45); Diary's denser card stack is a good candidate to check whether it reproduces here too, and whether it's worse given more scrollable content | | | P2 |
| DIARY-CHROME-TC-015 | Footer renders identically whether reached via Summary or Diary | Both pages loaded | 1. Compare footer content/layout on both pages | Identical — confirms genuine shared-component reuse; flag any divergence as a real inconsistency, not an intentional difference | | | P3 |
| DIARY-CHROME-TC-016 | External footer links open appropriately (new tab vs. same tab) | Footer visible | 1. Inspect each external link's `target` attribute | **Judgment call:** external destinations (App Store, Google Play, Privacy policy if hosted elsewhere) opening in a new tab preserves the user's app session; same-tab navigation away from the app is arguably worse UX — note as an opinion if inconsistent across links, not an automatic bug | | | P4 |

### J.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-CHROME-TC-017 | Footer typography/spacing consistent with the rest of the design system | Footer visible | 1. Compare footer text/link styling to Diary's own card typography | Consistent design-system treatment, not a visually distinct "bolted-on" footer | | | P4 |
| DIARY-CHROME-TC-018 | Tagline ("Sweat now, Shine later.") renders correctly and matches brand voice | Footer visible | 1. Read the tagline | Correct text, no typo, consistent with brand tone — judgment call on tone if it seems off | | | P4 |
| DIARY-CHROME-TC-019 | QR code sizing/legibility | Footer visible | 1. Visually inspect QR code size and print/screen clarity | Large and clear enough to plausibly scan (not so small it's unusable) | | | P3 |
| DIARY-CHROME-TC-020 | Footer link grouping/spacing is visually organized | Footer visible | 1. Inspect layout of link groups | Logically grouped and spaced, no crowding or ambiguous grouping | | | P4 |
| DIARY-CHROME-TC-021 | Grammar/copy review of all footer/header text | Footer and header both visible | 1. Read every string | No typos/grammatical errors | | | P4 |

### J.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-CHROME-TC-022 | Footer is marked up as a landmark region | Footer visible | 1. Inspect DOM for a `<footer>` element or `role="contentinfo"` | Present — helps screen-reader users jump directly to footer content | | | P3 |
| DIARY-CHROME-TC-023 | All footer links have accessible names and are keyboard-operable | Footer visible | 1. Tab through all footer links 2. Activate a few via Enter | All reachable and correctly labeled, no icon-only unlabeled links | | | P2 |
| DIARY-CHROME-TC-024 | Active nav tab is programmatically indicated, not just visually | On Diary page | 1. Inspect the active nav tab's DOM (per TC-002's finding) | If a tab IS shown as active, it should have `aria-current="page"` (or equivalent) so screen-reader users also know which section they're in | | | P2 |
| DIARY-CHROME-TC-025 | "Go back to the Top" moves keyboard focus, not just scroll position | Footer visible, page scrolled down | 1. Activate "Go back to the Top" via keyboard 2. Check `document.activeElement` | Focus moves to a sensible top-of-page target (e.g. the main heading or a skip-link anchor), not left stranded at the (now off-screen) footer button — a real, common a11y gap for "back to top" links | | | P2 |
| DIARY-CHROME-TC-026 | Chat widget is keyboard-accessible and doesn't trap focus unexpectedly | Chat widget available | 1. Tab to open it 2. Tab through its contents 3. Confirm Escape or a close button returns focus to the page | Fully keyboard-operable, no focus trap left dangling after closing | | | P2 |
| DIARY-CHROME-TC-027 | Color contrast of footer text/links | Footer visible | 1. Check contrast ratio of footer text against its background | Meets WCAG AA (4.5:1 text) — footers often use lower-contrast muted text, worth checking explicitly | | | P3 |

## K. Cross-date regression (multi-card, multi-day sweep)

**Purpose:** every card section above has its own single-card "does this stay correct across dates"
case, but those were tested in isolation. This section does two things those can't: (1) a genuine
**full-page** sweep — load a day and check all 9 cards together in one pass, which catches bugs that
only appear from state leaking between cards or across a longer sequence of navigations, not from any
one card tested alone; and (2) a **consolidated date-attribution regression matrix** — Bug #1 proved
Water misattributes a prior day's data to Today, but no other card has yet been explicitly checked for
the *same* symptom. This section closes that gap directly, one row per card. This section is purely
functional/data-integrity — there's no separate UI/UX or Accessibility table here, since visual design
doesn't vary by date.

### K.1 Functional & Data-Integrity

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-XDATE-TC-001 | Full 7-consecutive-day sweep — every card matches its own day's real data, checked together | 7 consecutive days with known/distinct data where possible | 1. Starting from Today, click "Previous day" 6 times, recording ALL 9 cards' values at each stop (not just one card at a time) 2. Compare each day's full snapshot against that day's known real data | Every card on every day matches its own day's real data — this is the master regression check; a failure here that wasn't caught by any single-card test would indicate a cross-card state-leak bug | | | P1 |
| DIARY-XDATE-TC-002 | Non-sequential ("random access") date jumps do not leave stale data from a previously-viewed day | On Diary page, Today | 1. Navigate Today → 5 days back → 2 days forward → 3 days back (deliberately non-monotonic) 2. At each stop, check all 9 cards | Every stop shows that day's own correct data — no card shows a value left over from an intermediately-visited day (a caching/state-reset bug that a purely sequential test could miss) | | | P1 |
| DIARY-XDATE-TC-003 | [Regression matrix] Snapshot's Steps/Active Minutes do NOT share Water's cross-day misattribution symptom | A day confirmed to have 0 Steps/Active Minutes logged, adjacent to a day with known non-zero values | 1. Load the zero day 2. Compare Snapshot's values to the adjacent day's | Zero day shows genuine 0/goal, 0% — does NOT echo the adjacent day's non-zero value (confirms this card is not affected by the same root-cause bug family as Water) | | | P1 |
| DIARY-XDATE-TC-004 | [Regression matrix] Calorie Ledger's Meals/Resting/Active/Balance do NOT share Water's misattribution symptom | A day confirmed to have 0 for all four values, adjacent to a day with known non-zero values | 1. Load the zero day 2. Compare all four values to the adjacent day's | Zero day shows genuine 0/0/0/0 — does NOT echo the adjacent day's values | | | P1 |
| DIARY-XDATE-TC-005 | [Regression matrix] Sleep does NOT share Water's misattribution symptom | A day confirmed to have no sleep logged, adjacent to a day with a known logged duration | 1. Load the no-sleep day 2. Compare to the adjacent day's duration | No-sleep day shows genuine "No Data" — does NOT echo the adjacent day's duration (consistent with this card's previously-confirmed clean result) | | | P1 |
| DIARY-XDATE-TC-006 | [Regression matrix] Vitals' Mood and Weight do NOT share Water's misattribution symptom | A day confirmed to have no mood/weight logged that specific day, adjacent to a day with known values | 1. Load that day 2. Compare Mood/Weight to the adjacent day's values, and to Section I's "carries-forward vs. resets" finding (TC-016) | Behavior matches whichever model Section I's TC-016 determined is correct (carry-forward or daily reset) — flag as a NEW issue only if the value matches an adjacent day's data in a way that contradicts that established model | | | P1 |
| DIARY-XDATE-TC-007 | [Regression matrix — already covered, cross-referenced here] Food Log and Activities do not share Water's misattribution symptom | See Section D (DIARY-FOOD-TC-002) and Section H (DIARY-ACT-TC-012) | 1. No new steps — this row exists to make the cross-card matrix complete in one place | Cross-reference only; see those sections for the actual test execution | | | P1 |
| DIARY-XDATE-TC-008 | [Regression matrix — known FAILING, cross-referenced here] Water is the confirmed exception | See Section F (DIARY-INTAKE-TC-001–006) | 1. No new steps — this row exists to make the cross-card matrix complete in one place | Cross-reference only — this is the one card where the symptom is confirmed present (Bug #1) | | | P1 — currently known FAILING |
| DIARY-XDATE-TC-009 | [Regression matrix] Distance card — not currently applicable | Distance rows are empty on all tested days (per Section G) | 1. No misattribution is testable while the card has no populated data at all | N/A until Section G's TC-001–003 (logging a matching-category activity) produce a populated day to test against | | | P4 — blocked pending Section G's open question |
| DIARY-XDATE-TC-010 | Full-page sweep across a month boundary | Navigate to the 1st of the current month | 1. Click "Previous day" once (crossing into the prior month) 2. Check all 9 cards | Header shows the correct prior-month date; every card shows that specific day's own correct data (not a repeat of the 1st's data) | | | P2 |
| DIARY-XDATE-TC-011 | Full-page sweep across a year boundary | Navigate to 1 January of the current year | 1. Click "Previous day" once (crossing into the prior year) 2. Check all 9 cards | Header shows 31 December of the prior year correctly; every card shows that day's own correct data | | | P2 |
| DIARY-XDATE-TC-012 | Full-page sweep across a leap day | Navigate to 1 March 2024 | 1. Click "Previous day" once (landing on 29 Feb 2024) 2. Check all 9 cards | No crash on any card; each renders a valid state (populated or empty) for the leap day | | | P3 |
| DIARY-XDATE-TC-013 | Full-page graceful degradation on a far-past date | Navigate roughly 1 year back | 1. Load that date 2. Check all 9 cards together | Every card shows a valid empty state — none partially crash/error while others load correctly (a mixed pass/fail across cards on the same page load would itself be a notable inconsistency) | | | P2 |
| DIARY-XDATE-TC-014 | Full round-trip: after an extensive multi-day sweep, returning to Today restores the exact original state | Following TC-001 or TC-002's extensive navigation | 1. Return to Today via repeated "Next day" clicks 2. Compare every card against the very first Today baseline captured at the start of this session | Every card matches the original baseline exactly — confirms no cumulative state corruption after a long sequence of navigations, which is a materially different (and more thorough) check than a single Previous→Next round-trip | | | P1 |

## L. Responsiveness (desktop/laptop/iPad/mobile)

**Breakpoints used:** Mobile — 390×844 (iPhone standard, primary target), 360×800 (Android
standard), 375×667 (iPhone SE, smallest common — most likely to reveal cramped-layout bugs the other
two don't). iPad — 768×1024 portrait, 1024×768 landscape. Laptop — 1366×768, checked on **both**
Windows (Chrome/Edge) and macOS (Safari/Chrome) since OS-level rendering (scrollbar behavior, font
rendering, native picker controls) can diverge at an identical viewport size. Desktop — 1440×900
(standard) and 1920×1080+ (ultra-wide check).

**Ordering note:** per explicit direction, mobile gets the deepest coverage and comes first in this
table, followed by iPad, then laptop (with its OS cross-check), then desktop last — this inverts the
usual desktop-first convention deliberately.

### L.1 Layout & Functional

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-RESP-TC-001 | Full page renders without horizontal overflow at primary mobile width | Diary page, a data-rich day | 1. Set viewport to 390×844 2. Scroll through the entire page | No horizontal scrollbar; no card content wider than the viewport | | | P1 |
| DIARY-RESP-TC-002 | Every card stacks cleanly in a single column with no clipping/overlap at mobile width | Same as TC-001 | 1. Inspect each of the 9 cards + header + footer individually while scrolling | No card's content is clipped, cut off, or overlapping an adjacent card/element | | | P1 |
| DIARY-RESP-TC-003 | Date-nav controls remain usable at mobile width | 390×844 | 1. Inspect Previous/label/Next group 2. Tap Previous and Next | No icon overlap with the heading/subtitle; both controls remain tappable and functional | | | P1 |
| DIARY-RESP-TC-004 | All "Log X"/"Add X" buttons remain reachable and meet the mobile touch-target minimum | 390×844, across all cards | 1. Inspect and tap each write-path button (Log water, Add Sleep Data, Log weight, Log mood, Log meals) | Each is tappable, ≥44×44px effective hit area, no two adjacent targets close enough to cause mis-taps | | | P1 |
| DIARY-RESP-TC-005 | Every modal (Log Water, Add Sleep Data, Log Weight, Log Mood) fits and functions correctly at mobile width | 390×844 | 1. Open each modal in turn 2. Check it fits the viewport (or scrolls internally if content exceeds height) and the Save button is reachable | No modal has its Save/primary action cut off below the visible viewport; internal scrolling works if content is tall (e.g. Log Water's stepper + glasses display) | | | P1 |
| DIARY-RESP-TC-006 | On-screen keyboard does not permanently hide the active input or Save button | 390×844, a modal with a text/numeric field open (e.g. Log Weight) | 1. Focus the input to trigger the on-screen keyboard (real device or emulator keyboard simulation) 2. Check visibility of the field and Save button | Page/modal scrolls to keep the focused field and the Save button visible above the keyboard — neither is permanently obscured | | | P1 |
| DIARY-RESP-TC-007 | Extra-small mobile width (iPhone SE, 375×667) does not reveal additional clipping/overlap | 375×667 | 1. Repeat TC-001–002's checks at this narrower/shorter viewport | No NEW clipping/overlap issues beyond what's seen at 390×844 — this size is specifically chosen because it's the most likely to expose cramped-layout bugs the standard mobile size doesn't | | | P1 |
| DIARY-RESP-TC-008 | Common Android width (360×800) renders correctly | 360×800 | 1. Repeat TC-001–002's checks | No layout issues specific to this slightly narrower width | | | P2 |
| DIARY-RESP-TC-009 | Mobile landscape orientation does not break layout | 844×390 (mobile, rotated) | 1. Load the Diary page 2. Check card stacking, modal usability | Layout adapts without breaking — modals remain usable, no content becomes unreachable via scroll | | | P2 |
| DIARY-RESP-TC-010 | Safe-area insets are respected on notched/home-indicator devices | 390×844 (or a real notched device/simulator) | 1. Scroll to the bottom of the page 2. Check the footer and floating Chat widget against the home-indicator safe area | Neither the footer's bottom content nor the Chat widget bubble is obscured by or overlapping the device's home-indicator/safe-area zone | | | P2 |
| DIARY-RESP-TC-011 | Chat widget does not cover card content/buttons when scrolled to the bottom, at mobile width | 390×844, scrolled to the Vitals/footer area | 1. Scroll fully to the bottom 2. Check for overlap between the floating Chat bubble and the last card's interactive elements | No overlap that blocks a button/tap target — this directly re-verifies Section J's TC-014 finding specifically through a full responsiveness lens, since Diary's dense card stack is the most likely page to reproduce the known Chat-widget-overlap pattern (Bugs #24/#28/#45) | | | P2 |
| DIARY-RESP-TC-012 | Touch-target spacing prevents mis-taps between adjacent controls | 390×844, Log Water modal open (glass stepper) | 1. Inspect spacing between "Add a glass"/"Remove a glass" buttons | Sufficient spacing that a real thumb tap reliably hits the intended control, not its neighbor | | | P2 |
| DIARY-RESP-TC-013 | Sticky/fixed header (if any) does not overlap content while scrolling on mobile | 390×844 | 1. Scroll down and back up 2. Check whether the header is fixed/sticky, and if so, whether it overlaps card content | If sticky, no card content is hidden beneath it; if not sticky, note that as the actual behavior rather than assuming it should be | | | P3 |
| DIARY-RESP-TC-014 | iPad portrait renders with an appropriate (not just a stretched-phone) layout | 768×1024 | 1. Load the Diary page 2. Inspect card layout/width usage | **Judgment call:** cards use the available width sensibly (e.g. a wider single column with sensible max-width, or a light multi-column treatment) rather than either a cramped phone-width column or an awkwardly over-stretched one | | | P2 |
| DIARY-RESP-TC-015 | iPad landscape renders correctly | 1024×768 | 1. Load the Diary page 2. Inspect layout | No awkward mid-breakpoint layout (excessive whitespace or squeezed content); modals remain usable | | | P2 |
| DIARY-RESP-TC-016 | Touch interactions work correctly at iPad size | 768×1024 or 1024×768 | 1. Repeat key write-path taps (Log water, Add Sleep Data) at iPad size | Same tap-target/modal-usability standards as mobile are met at this size too | | | P3 |
| DIARY-RESP-TC-017 | Laptop breakpoint renders correctly on Windows (Chrome/Edge) | 1366×768, Windows | 1. Load the Diary page 2. Inspect full layout | No overflow/clipping; scrollbar presence (Windows' persistent scrollbar reduces usable content width slightly) does not cause any card to clip | | | P2 |
| DIARY-RESP-TC-018 | Laptop breakpoint renders identically on macOS (Safari/Chrome) | 1366×768, macOS | 1. Repeat TC-017's checks on macOS | Layout matches Windows' rendering; flag any OS-specific divergence (e.g. macOS's overlay scrollbars revealing slightly more content width, or a font-rendering difference causing text overflow on one OS but not the other) | | | P2 |
| DIARY-RESP-TC-019 | Native picker controls (if used) render consistently across Windows and macOS | 1366×768, both OSes, a modal with a time/date input (e.g. Sleep) | 1. Open the same modal on both OSes 2. Compare the picker control's appearance/behavior | If the app uses native OS picker controls, note any OS-specific visual/behavioral divergence — informational, not necessarily a bug, unless it breaks usability on one OS | | | P3 |
| DIARY-RESP-TC-020 | Standard desktop width renders correctly | 1440×900 | 1. Load the Diary page 2. Inspect full layout | No overflow/clipping; consistent with prior desktop testing on other pages | | | P3 |
| DIARY-RESP-TC-021 | Ultra-wide desktop does not stretch content awkwardly | 1920×1080 (or wider, e.g. 2560×1440) | 1. Load the Diary page | Content is either constrained to a sensible max-width and centered, or genuinely uses the extra space well — flag as a P3 polish issue if cards stretch edge-to-edge with excessive whitespace between elements | | | P3 |
| DIARY-RESP-TC-022 | Browser zoom (125%/150%/200%) at desktop width does not break layout | 1440×900, various zoom levels | 1. Zoom to 125%, 150%, 200% in turn 2. Inspect layout at each | Text/cards reflow or scale sensibly; no overlapping text, no cut-off buttons at any tested zoom level | | | P2 |

### L.2 UI/UX & Content

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-RESP-TC-023 | Font sizes scale appropriately across breakpoints, remaining readable on mobile | Mobile, iPad, desktop compared | 1. Compare heading/body font sizes across breakpoints | No text shrinks below a comfortably readable size on mobile; scaling is deliberate, not just a uniform shrink of a desktop layout | | | P3 |
| DIARY-RESP-TC-024 | Card padding/margins scale sensibly rather than staying desktop-identical on mobile | Mobile vs. desktop compared | 1. Compare card internal padding/spacing at both sizes | Mobile uses tighter, mobile-appropriate spacing — not identical desktop padding crammed into a narrow viewport | | | P3 |
| DIARY-RESP-TC-025 | Icons/images remain crisp across standard and high-DPI (retina) displays | Standard vs. retina display or emulation | 1. Inspect icon/image sharpness on both | No blurriness/pixelation on high-DPI displays (icons use appropriate resolution/vector assets) | | | P4 |

### L.3 Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-RESP-TC-026 | Content reflows at 400% zoom without requiring two-dimensional scrolling (WCAG 1.4.10 Reflow) | Desktop viewport equivalent to 1280px wide, zoomed to 400% | 1. Zoom to 400% 2. Check whether both horizontal AND vertical scrolling are required to read content | Content reflows to a single scrollable column (vertical scroll only) — no requirement to scroll both horizontally and vertically to read any given piece of content | | | P2 |
| DIARY-RESP-TC-027 | Touch targets meet the WCAG 2.5.8 minimum at mobile width | 390×844 | 1. Measure all interactive element hit areas | Meets ≥24×24 CSS px minimum (WCAG AA), ideally ≥44×44px — consistent with, not a duplicate of, the per-card touch-target checks already noted throughout this file; this row is the single consolidated pass across the WHOLE page at once | | | P3 |
| DIARY-RESP-TC-028 | App does not force a single display orientation without justification (WCAG 1.3.4 Orientation) | Mobile device/emulator | 1. Rotate between portrait and landscape | Content remains usable in both orientations (per TC-009) — the app should not lock to portrait-only unless there's an essential reason (there isn't an obvious one here) | | | P3 |

## M. Global accessibility (page-level)

**Scope:** everything that applies across the WHOLE page and can't be owned by any single card's own
Accessibility table (each card section already covers its own accessible names, focus traps within its
own modal, contrast, and touch targets). This section covers page structure, full-page keyboard/screen-
reader traversal, and OS/browser-level accessibility preferences (reduced motion, forced colors,
text-only zoom) — one consolidated table, since the entire section is accessibility-focused rather than
needing a further UI/UX split.

### M.1 Global Accessibility

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DIARY-A11Y-TC-001 | Full-page keyboard Tab order matches the visual top-to-bottom reading order | On Diary page, a data-rich day | 1. Starting from the top of the page, Tab through every interactive element on the entire page 2. Note the order | Tab order follows the visual layout logically (header → date-nav → cards top-to-bottom → footer); no element is skipped, no keyboard trap anywhere, no jump that contradicts the visual order | | | P1 |
| DIARY-A11Y-TC-002 | Heading hierarchy is logical, with no skipped levels | On Diary page | 1. Inspect the DOM's heading structure (h1/h2/h3...) | A single `<h1>` for "Diary" (or the page's true top-level heading), card headings at a consistent, correctly-nested level (e.g. all `<h2>`), no level skipped (e.g. h1 straight to h3) | | | P2 |
| DIARY-A11Y-TC-003 | Landmark regions exist for screen-reader quick-navigation | On Diary page | 1. Inspect DOM for `<main>`, `<header>`, `<footer>`, `<nav>` (or equivalent ARIA landmark roles) | All major page regions are properly landmarked, allowing screen-reader users to jump directly between them instead of reading linearly through everything | | | P2 |
| DIARY-A11Y-TC-004 | All form inputs across every modal have properly associated labels, not placeholder-only | On Diary page, each modal opened in turn (Log Water, Add Sleep Data, Log Weight, Log Mood) | 1. Inspect each input's label association (`<label for>`, `aria-label`, or `aria-labelledby`) | Every input has a real programmatic label — placeholder text alone (which disappears on input and isn't reliably read by all screen readers) is not the sole labeling mechanism anywhere | | | P1 |
| DIARY-A11Y-TC-005 | Validation error messages are programmatically associated with their field and announced | Following a rejected input (e.g. Vitals' TC-014 invalid weight value) | 1. Trigger a validation error 2. Inspect whether the error is linked via `aria-describedby`/`aria-invalid` and whether it's announced | Error is both visually shown AND programmatically associated + announced to screen readers — not conveyed by a red border/color alone | | | P1 |
| DIARY-A11Y-TC-006 | Success confirmation after saving is announced to screen readers, not purely visual | After successfully saving Sleep/Weight/Mood/Water | 1. Save an entry 2. Check for a toast/snackbar or other confirmation, and whether it's in an `aria-live` region | If a success confirmation exists, it's announced via `aria-live`; if none exists at all (silent success), note that as a UX gap worth flagging, since screen-reader users would have no confirmation the save succeeded beyond the card updating | | | P2 |
| DIARY-A11Y-TC-007 | Visible focus indicator exists on every interactive element site-wide | On Diary page | 1. Tab through the entire page 2. Check that each focused element has a clearly visible focus outline/ring | No interactive element loses its focus indicator (a common issue when a CSS reset strips default browser focus outlines without replacing them) | | | P1 |
| DIARY-A11Y-TC-008 | `html lang` attribute is correctly set | On Diary page | 1. Inspect the `<html>` element's `lang` attribute | Set to the correct language code (e.g. `en`), ensuring correct screen-reader pronunciation | | | P3 |
| DIARY-A11Y-TC-009 | Reduced-motion preference is respected for animations/transitions | OS/browser set to `prefers-reduced-motion: reduce` | 1. Enable reduced-motion at the OS level 2. Observe modal open/close transitions and any progress-ring fill animation | Animations are reduced/removed (e.g. instant modal appearance instead of a slide/fade, ring fills instantly rather than animating) — respects the user's OS-level preference | | | P2 |
| DIARY-A11Y-TC-010 | Page remains usable in forced-colors/high-contrast mode | Windows High Contrast Mode (or `forced-colors: active` emulation) enabled | 1. Load the Diary page 2. Check legibility of all cards, buttons, and the progress ring | All text remains legible, buttons/interactive elements remain visually distinguishable and operable — no content that relies purely on a background-color fill (e.g. the progress ring, or Water's fill level) becomes invisible/indistinguishable in forced-colors mode | | | P2 |
| DIARY-A11Y-TC-011 | Full-page screen-reader read-through is coherent | On Diary page, VoiceOver/NVDA active (or accessibility-tree inspection as a proxy) | 1. Read through the entire page linearly with a screen reader | The page makes logical sense read top-to-bottom — no orphaned/confusing announcements, no critical information conveyed only visually (e.g. color-only status) that a screen-reader user would miss | | | P2 |
| DIARY-A11Y-TC-012 | Text-only browser zoom (increased default font size, not full-page zoom) does not break layout | Browser's text-size/font-size setting increased (distinct from the pinch/ctrl-scroll zoom already tested in Section L) | 1. Increase the browser's default text size setting 2. Reload the Diary page | Layout adapts without overlapping text or cut-off buttons — a distinct check from Section L's page-zoom testing, since some browsers apply text-only scaling differently from full-page zoom | | | P3 |
| DIARY-A11Y-TC-013 | Skip-to-content link exists and functions for keyboard users | On Diary page | 1. Load the page fresh 2. Press Tab once, immediately, before interacting with anything else | A "Skip to main content" (or equivalent) link is the first focusable element, and activating it moves focus past the repeated header/nav chrome directly to the page's main content — if absent, log as a genuine gap for users who navigate the same header on every page load | | | P3 |
