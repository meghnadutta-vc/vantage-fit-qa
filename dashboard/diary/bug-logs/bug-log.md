# Vantage Fit Web — Diary Page Bug Log

Environment: Production — `https://fitvantage.vantagecircle.com/ng/fit/summary/diary`
Account: Demo/test tenant (CRUD safe)

---

## Section A: Page header & date navigation — findings

## Bug #1 [Functional/Data - P1]
[Diary → Intake card — Water value does not reset per-day; backend misattributes a prior day's water log to Today, confirmed via raw API response]
Navigating the Diary date picker across three consecutive days produced:
- **13 July** (no water ever logged): Water correctly shows **"0/ 2.5 L"**.
- **14 July** (water was genuinely logged this day, per prior Quick Add testing): Water shows
  **"25.36/ 2.5 L"** (itself already a known wrong unit/value — see Bug #34 in
  `dashboard/quick-add/bug-logs/bug-log.md` — expected "0.75/ 2 L").
- **15 July / Today** (no water logged today at all): Water **also** shows **"25.36/ 2.5 L"** —
  identical to the 14th's value, despite zero water being logged today.
Reproduced this exact sequence twice via two different navigation paths (Previous-day chain
15→14→13, and a fresh Next-day chain 13→14→15), and confirmed with a **hard page reload** while
sitting on Today — the wrong "25.36/ 2.5 L" value persisted through the reload, ruling out a
client-side cache/SPA-navigation artifact.

**Escalated with hard API evidence**: inspected the raw response body of
`POST /vantagefit/api/v1/today/overview` (the endpoint backing this page) while on Today (15 July).
It returns:
- `"nutritionData".."Water"`: `{"value":"25.36 fl oz","rawValue":25.36}` — confirms the backend
  itself, not just the frontend cache, is returning the 14th's water figure for the 15th's request.
- `"activitiesData"` (today's activity feed) includes an entry
  `{"dataType":"Water \n(11:55 AM)","value":"750 count"}` — a water-logging event timestamped
  "11:55 AM" appears in **today's** activities list, despite no water being logged today at any
  time. 750 is the real ml amount originally logged on the 14th (matches 25.36 fl oz exactly).
This is conclusive: the backend is misattributing a prior day's water-log record to Today's date,
both in the aggregate nutrition total and in the day's individual activity/event feed.

**Separately, and not yet reconciled with the above**: opening the "Log Water" modal itself on Today
shows a third, different, even larger wrong value — **"5000 ml / 20 of 8 glasses / Daily goal
reached"** (glasses stepper maxed at 20, "Add a glass" disabled). 5000 ml does not match the 750 ml
figure found in the API response above via any obvious unit conversion, so this appears to be a
separate discrepancy from the same root problem, not simply the same wrong value re-displayed.
Reproduced consistently across two separate modal opens.

Expected: A day with zero water logged should show "0/ 2.5 L" and an empty activities feed for water
(as 13 July correctly does); the Log Water modal should pre-fill 0 ml / 0 glasses on a day with no
entry.
Actual: Today's aggregate total, activity feed, AND the Log Water modal's own pre-fill all show
different, mutually-inconsistent non-zero values, none of which is the correct 0.
Note/Doubt: Raised from P2 to **P1** given this is now confirmed via raw API inspection (not just UI
observation) to be a genuine backend date-attribution bug, and it corrupts three different surfaces
(aggregate total, activity feed, and the logging modal's pre-fill) with three different wrong
numbers. Recommend the dev team check the `today/overview` endpoint's date-scoping logic for the
water/nutrition query specifically, and separately investigate where the Log Water modal sources its
"5000 ml / 20 glasses" figure from, since it doesn't match the overview API's own 25.36/750 figure.
Evidence: evidence/diary_01_water_stale_today.png, evidence/diary_02_water_modal_wrong_5000ml.png;
raw API response body captured via `browser_network_request` on `POST /today/overview`, see
conversation trace; reproduced across three separate date navigations, a hard reload, and two
separate Log Water modal opens.

---
## Section B: Snapshot card (Diary's own copy) — findings

No new bugs filed this section — see Notes below for the one reproduction of an existing issue.

---
### Notes / Doubts (not bugs) — Section B
- **Reproduces the Summary area's Bug #3 (redundant duplicate tab stop)**: the Diary Snapshot card
  uses the identical `<article role="button" tabindex="0" aria-label="View Trends" class="card
  snapshot is-tappable">` wrapper pattern found on Summary, with a nested real `<button>` (chevron-right
  icon) whose text content is also "View Trends" — confirmed via DOM inspection. Same root cause as
  `dashboard/summary/bug-logs/bug-log.md` Bug #3; not re-filed as a new bug number here since it's the
  same shared component/pattern, just with a different context-appropriate label ("View Trends" here
  vs. "Open Diary" on Summary).
- **Unlike Summary's version, this card's label accurately matches its destination**: clicking/
  activating it navigates to `/ng/fit/activity-stats` (the dedicated Trends stats page), consistent
  with the "View Trends" label. This is correct, contextual reuse of the shared card component — no
  functional defect, only the inherited redundant-tab-stop issue noted above.
- **Steps/Active Minutes values match Summary's Snapshot exactly** for the same day (0/10000 0%;
  30/32 mins 94%) — confirms no cross-page data inconsistency for these two stats specifically.
- **No motivational text paragraph** ("You have been among the top X%...") appears in this Diary copy
  of the Snapshot card, unlike Summary's version. Confirmed intentional simplification for this
  context, not a rendering gap — the stats list and card wrapper both render completely and
  correctly otherwise.

---
## Section C: Calorie Ledger — findings

No new bugs filed this section — see Notes below for one reproduction and several positive findings.

---
### Notes / Doubts (not bugs) — Section C
- **Arithmetic is correct**: Meals − Resting − Active = Balance verified exactly across multiple
  reloads (e.g. 0 − 1,058 − 384 = -1,442, matching the displayed Balance).
- **"Resting" kcal climbs slowly over time within the same day** (observed 1,035 → 1,047 → 1,051 →
  1,055 → 1,058 kcal over ~20 minutes of testing) — this is consistent with a live-accumulating
  Resting Metabolic Rate calculation (calories burned at rest so far today, recalculated based on
  elapsed time), not an error: the rate of increase (~63 kcal/hour) is plausible for a daily RMR in
  the ~1,500 kcal/day range. Verified this is a smooth, steady climb, not random jumps — confirms
  intentional live-tracking behavior, not a bug.
- **"Learn more" link correctly opens a detailed "Caloric Balance" educational modal** despite having
  `href="#"` — the href is a placeholder but a real JS click handler opens genuine, well-written
  content about Resting Metabolic Rate. Not a dead link in practice.
- **Meal logging is app-only** (Food Log's own "Log meals" button opens the same "Continue this in
  the Vantage Fit app" mobile hand-off modal used elsewhere) — so Meals/Balance live-update behavior
  cannot be tested from the web at all; this is consistent with the same Log Meal pattern already
  documented in `dashboard/quick-add/`.
- **Reproduces the known no-focus-trap pattern** (see quick-add area's Bug #3) via this new entry
  point: `document.activeElement` stays on the "Log meals" trigger button after the hand-off modal
  opens, rather than moving focus into the modal. Not re-filed as a new bug — same root cause,
  confirmed present here too.

---
## Section E: Sleep card — findings

No bugs found this section — a clean positive result, in useful contrast to earlier stale-data findings.

---
### Notes / Doubts (not bugs) — Section E
- **Sleep card updates in-place immediately after saving**, no navigation-away-and-back or hard
  reload required: logged 8h 0m (9:00 PM–5:00 AM default) via the card's own "Add Sleep Data" button
  (a real web modal, unlike meal logging), and the card instantly switched from "No Data" to
  "8 hrs 0 mins / Total sleep duration" the moment the modal closed.
- **Persistence confirmed via a hard reload** — the value remained correct, ruling out a stale-cache
  false positive.
- This is a genuine positive contrast with Bug #2 (Summary area) and Bug #4 (Summary area), where
  Snapshot/League data required a navigate-away-and-back or didn't update at all — Sleep's own data
  path does not share that gap.
- Empty state ("No Data" / "Track your sleep to see insights") rendered correctly before logging.

---
## Section G: Distance — findings

No bugs found this section.

---
### Notes / Doubts (not bugs) — Section G
- **"mile" unit label is plain static text, not interactive** — no unit toggle exists on this card
  (confirmed via DOM: it's a `generic`, not a button/select). Not filed as a bug since there's no
  evidence a toggle is meant to exist here; noted as a scope observation only.
- **Moved / Jog-Run / Cycling all show "—" (empty) on every day tested**, including 14 July which had
  7 activities logged (210 Active Minutes, Hiking/Swimming/Yoga etc.) and 13/15 July with 0 activities.
  This card appears to track a separate data source from manually-logged Activities (likely
  device/GPS/pedometer-based passive movement, which this demo web account never populates) rather
  than aggregating logged-activity distances. Not filed as a bug — did not find evidence this card is
  supposed to reflect manually-logged activities, so treating the consistent empty state as a data-
  source gap for this test account rather than a defect. Worth a product question if a real device
  account is ever available to test against.

---
## Section H: Activities — findings

No new bugs filed this section — see Notes for one confirmed reproduction.

---
### Notes / Doubts (not bugs) — Section H
- **"N logged" counter is accurate**: 14 July correctly shows "7 logged" (matching the real 7 activities
  from that day's original quick-add testing session); Today (15 July) correctly shows "1 logged"
  (Hiking); 13 July correctly shows "No activities logged."
- **Individual activity rows are accurate**: name, time, and duration/distance all matched the real
  logged data on 14 July (Hiking 11:15 AM/30 min/5,000m; Swimming 11:22 AM/45 min; Yoga 11:25 AM/45 min).
- **Reproduces the known "View all" bug** (quick-add area's Bug #9): clicking the small icon button
  next to "7 logged" opens the "add new activity" Log Activity picker instead of showing the full
  7-item list — confirmed from this Diary-native entry point too, not just the one originally found.
  Not re-filed as a new bug, same root cause.
- **No edit/delete affordance on logged activity rows**: clicking the "Hiking" row does nothing (no
  modal, no navigation). Matches the same product/scope gap already noted in
  `dashboard/quick-add/bug-logs/bug-log.md` — treated as a known scope gap, not a new defect.

---
## Section I: Vitals — findings

No bugs found this section — clean, positive results across the board.

---
### Notes / Doubts (not bugs) — Section I
- **Mood**: "Edit mood" correctly pre-fills the existing value ("Not Good" shown as pressed/selected),
  heading reads "How are you feeling?", submit button correctly labeled "Update" (not "Save").
- **Weight**: "Edit weight" correctly pre-fills "74.6 kg" (= 164.4 lbs, the real logged value) with
  "Same as last log" shown, submit button correctly labeled "Update weight".
- **Cross-page consistency confirmed**: Diary's Vitals (Weight 164.4 lbs) matches Summary's own
  Vitals card exactly for the same underlying data — no discrepancy found here, in contrast to the
  Water intake bug (#1) and the League banner bug (Summary area's #4).
- **Heart Rate confirmed app-only**: "Log heart rate on the app" button — consistent with the
  already-documented mobile-hand-off pattern used elsewhere; not re-tested in depth here.

---
### Notes / Doubts (not bugs) — Section A
- **Date navigation genuinely loads correct historical data** for every other section tested:
  Steps/Active Minutes, Calorie Ledger (Active kcal, Resting kcal, Balance), Sleep, Activities list
  (count + individual entries), and Vitals (Mood, Weight) all correctly changed to reflect each
  day's real data when navigating with Previous/Next day — e.g. 14 July correctly showed "210/32
  mins" Active Minutes, "7 logged" Activities (Hiking/Swimming/Yoga...), Mood "Not Good", Weight
  "164.4 lbs"; 13 July correctly showed all-empty/zero states. Only Water (Bug #1 above) fails this
  check.
- **"Diary" heading + date subtitle correct**: "Today · 15 July 2026" matches the real system date
  exactly (cross-checked against `new Date()` during Summary-page testing this session).
- **Next day button correctly disabled on Today**, and correctly enables once a past day is selected.
- **The date range group's middle label ("Today"/"14 Jul"/etc.) is plain text, not interactive** — no
  date-picker/jump-to-date feature exists; navigation is strictly sequential day-by-day via
  Previous/Next. Not a bug — just a design/scope note (no jump-to-arbitrary-date affordance found).
