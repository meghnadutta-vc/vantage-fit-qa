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
## 2026-07-16/17 — Re-test of Bug #1 during smoke-suite execution (MATERIAL CORRECTION)

**Status: Bug #1's original "cross-day misattribution" diagnosis does not hold up under a fresh,
controlled re-test. Recommend re-classifying/re-scoping rather than treating the original write-up
above as still-accurate.**

**What was re-tested:** on a real, fresh Today (16 July 2026, confirmed via system clock), with Water
genuinely at 0 (verified via raw API `rawValue: 0` before logging anything), I logged an exact known
amount — 3 glasses (750 ml) — via the Log Water modal's own stepper, then inspected the raw
`today/overview` API response.

**Result:** the new `activitiesData` entry was `"Water (06:03 PM)"` — a timestamp matching the actual
moment of the test, genuinely belonging to Today. This directly contradicts the original bug's central
claim that Today's water gets attributed from a *different* day's stale record (the original write-up's
smoking-gun evidence was a "Water (11:55 AM)" entry appearing on a day it wasn't logged). A fresh,
controlled log on a real Today produced its own fresh, correctly-dated entry — no cross-day copying
observed.

**Why the original investigation likely reached the wrong conclusion:** 750 ml converts to exactly
25.36 fl oz — a fixed, unit-conversion fact, not a coincidence unique to any one day. If 750 ml (or the
same round test amount) was logged independently on both 14 July and 15 July during the original
investigation session (plausible, since QA test data commonly reuses round numbers like "3 glasses"),
**both days would correctly and independently show "25.36/2.5 L"** — which is exactly what was
observed — without requiring any cross-day data corruption at all. The original investigator likely
mistook two independent, correct, identical-by-coincidence values for one corrupted value copied
across days.

**What IS still confirmed live and real, independently of the above:**
1. **Unit-label mismatch (Bug #34 lineage):** the Water row's displayed numerator is fl-oz-derived
   (e.g. "25.36") but its denominator/goal is labeled "L" (e.g. "2.5 L") — an internally inconsistent,
   mixed-unit display. Reproduced live on a fresh 16 July log this session.
2. **Modal-goal vs. card-goal mismatch:** the Log Water modal itself states the daily goal as
   **2000 ml (8 glasses × 250 ml = 2 L)**, while the Intake card's own goal label reads **"2.5 L"** —
   two different stated goals for the same metric on the same page. Confirmed via direct modal
   inspection this session (not previously documented as a distinct issue).
3. **Modal pre-fill was CORRECT this run** (0 ml / 0 of 8 glasses / "2000 ml to goal") — the
   previously-reported "5000 ml / 20 of 8 glasses / Daily goal reached" pre-fill bug did NOT reproduce.
   This may mean that specific pre-fill glitch was itself transient/session-specific, or has since been
   fixed — worth a dedicated re-test rather than assuming either.

**Recommendation:** downgrade/re-file Bug #1 as two separate, narrower, still-real issues — the
unit-label mismatch (#1) and the modal/card goal mismatch (#2) — rather than a date-attribution defect.
The date-attribution mechanism as originally described could not be reproduced under a controlled test
and should not be relied upon as confirmed without further evidence (e.g. a controlled test using a
*distinctive*, non-round amount logged on one specific day, then checking whether an adjacent empty day
echoes that exact non-round number — this run only used a round, easily-coincidental amount).

**Evidence:** raw API response bodies captured via `browser_network_request` for `today/overview` on
16 July (before and after logging), 15 July, and 14 July during this session; see
`dashboard/smoke/diary-smoke.md` TC-008a/008b/008c for the full step-by-step trace.

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

---
## 2026-07-16/17 — Smoke-suite execution findings (new session)

## Bug #2 [Functional - P1]
[Diary → Sleep card — no edit affordance for an already-logged same-day entry]
On Today (16 July, fresh log), after successfully logging 8h 0m of sleep via "Add Sleep Data" (which
correctly updates the card in place), attempted to edit that entry by clicking the card's own duration
text/summary ("8 hrs 0 mins" / "Total sleep duration"). Nothing happened — no modal opened, no
navigation occurred, no visible affordance of any kind.
Expected: Some way to correct/edit an already-logged same-day sleep entry should exist, consistent with
Weight ("Edit weight") and Mood ("Edit mood"), which both correctly expose an edit path once logged.
Actual: Sleep appears write-once from this entry point on Diary — once logged for a day, there is no
discovered way to revise it short of contacting support or using the mobile app (unverified whether the
app offers this either).
Note/Doubt: Only the visible summary text was tried as a click target; a hover-revealed icon or a
different UI element not exposed to the accessibility tree could theoretically exist and wasn't
exhaustively searched pixel-by-pixel. Recommend a human QA pass with a mouse to hover every element of
the Sleep card before treating this as fully confirmed, though the automated attempt found nothing.
Evidence: reproduced once this session; see `dashboard/smoke/diary-smoke.md` TC-009b.

---
### Notes / Doubts (not bugs) — 2026-07-16/17 session
- **Weight modal pre-fills a "last known value" the card itself does not surface**: on a day with no
  weight logged yet (card shows "--"), opening "Log weight" pre-filled "74.8 kg / Your latest weigh-in"
  — a real prior value the card's own "--" display doesn't hint at. This directly answers Section I's
  open question (TC-016, "does Weight carry forward or reset?") for the CARD specifically: it resets to
  "--" per day, but the MODAL internally still knows and surfaces the last real value. Worth a product
  discussion on whether the card should instead show something like Summary's "164.4 lbs · Updated 14
  Jul" pattern instead of a bare "--", for consistency with Summary's own Vitals card.
- **Freshchat "Chat with us" widget renders at 0×0 dimensions on a 390×844 mobile viewport** (confirmed
  via `getBoundingClientRect()` on the iframe). Not asserted as a bug — could be an intentional mobile
  design choice ceding that screen space to the bottom tab bar — but flagged as needing product
  confirmation, since the alternative reading (mobile-web users cannot reach chat support at all) would
  be a real functional gap.
- **Mood's positive-response flow has an undocumented follow-up step**: selecting "Pretty Nice" (or
  presumably other positive moods) surfaces a "Nice — what made it good?" tag picker (Exercise,
  Education, Family, Food, Friends, Relationship, Work, Travelling, Other) before the entry is
  considered complete. This wasn't documented in prior test passes or in `diary.md`'s Section I —
  worth adding explicit coverage for this sub-flow (does it appear for ALL positive moods, is a tag
  required or optional, does the selected tag surface anywhere else in the app).
- **The page's real scroll container is Angular Material's `.mat-drawer-content`**, not
  `document.body`/`window` — confirmed via DOM inspection (`scrollHeight: 2304` vs. viewport
  `844`). Purely a technical note for automation authors, not a product-facing issue.
- **Bug #1 (Water) materially re-assessed this session** — see the dedicated section above this one.

---
## 2026-07-17 — P1-priority pass across diary.md (main suite)

## Bug #3 [Functional - P1]
[Diary → Activities card — does not update in place after logging a new activity]
On Today (17 July), logged a new activity ("Hiking", 30 min, 5.0 km distance) via Quick Add's Log
Activity flow while sitting on the Diary page. The `POST /vantagefit/api/v1/activity/save` call
returned 200 with `{"status_message":"Activity Saved Successfully","userActivityId":2296107}` —
confirmed saved server-side. Despite this, the Activities card continued to show "No activities
logged." with no visual change immediately after the modal closed.
Expected: The Activities card should update in place to show "1 logged" / the new Hiking row, matching
the good in-place-update pattern already confirmed for Sleep, Weight, and Mood on this same page.
Actual: No visible change until a hard reload (`page.goto` on the same URL) was performed, after which
the card correctly showed "1 logged" — Hiking, 9:57 AM, 30 mins, 5,000m.
Note/Doubt: This is the same root-cause family as Summary's already-documented Bug #2 (Snapshot/Trends
not refreshing after a new log without a hard reload/nav-away), now confirmed to also affect Diary's
Activities card specifically — not previously verified here (Section H's TC-002 was flagged "verify,
don't assume" and this is the direct answer: it requires a reload).
Evidence: `activity/save` response body captured via `browser_network_request`; before/after DOM
snapshots this session.

## ~~Bug #4~~ CORRECTED — Distance card behavior is app-only by design, NOT a bug (retracted)
[Diary → Distance card — originally filed as "does not reflect distance from a manually-logged
activity", now retracted after re-examining the Quick Add menu's own labeling]
**Correction:** the Quick Add menu (opened during this same session, see the option list captured
alongside "Log Activity") includes an entry **"Start Outdoor Workout — Track on app"**. This is the
same "Track on app" labeling convention already established and correctly identified elsewhere on this
page for Heart Rate ("Log heart rate on the app") — i.e. a clear, existing UI signal that a given
metric is populated exclusively via the mobile app, not manually loggable from web at all. "Start
Outdoor Workout" is almost certainly the actual GPS-tracked mechanism that feeds the Distance card's
Jog-Run/Cycling (and likely Moved) rows — not the "Log Activity" manual picker used in the original
test (which logged "Hiking" with a manually-entered 5.0km distance).
Original finding (for the record, superseded by the correction above): logged "Hiking" with an explicit
5.0 km distance via "Log Activity" (confirmed saved server-side — `activity/save` returned 200, and
the Activities card correctly showed "5,000m" for it after a hard reload). The Distance card's three
rows still showed "—", which was originally filed as an open functional question.
Corrected conclusion: this is **not a bug**. "Log Activity" (manual entry) was never the correct
mechanism to test the Distance card against — "Start Outdoor Workout" (Track on app) is, and it is
explicitly app-only, resolving Section G's original open question (DIARY-DIST-TC-001–003) as an
app-only limitation, the same category as Heart Rate, not a defect requiring product confirmation.
Lesson: when a UI element is explicitly labeled "Track on app" (or the equivalent "Log X on the app"
pattern), that is a definitive signal the feature is app-only — it should be treated the same as any
other confirmed app-only hand-off (like meal logging), not investigated further as an ambiguous web bug.
`diary.md`'s Section G and the coverage log have been updated to reflect this correction.

---
### Notes / Doubts (not bugs) — 2026-07-17 P1 pass
- **Not all manually-loggable activities have a distance field.** Checked two Log Activity forms
  directly: "Hiking" has a Distance stepper (km/mi toggle, default 5.0); "Post Coffee Walk" has none
  (only Date/Time/Duration/Calories/Active-Minutes-conversion). This is itself a plausible, non-bug
  design choice (some activity types genuinely have no meaningful distance, e.g. static/indoor
  workouts) but worth knowing when devising further Distance-card test scenarios.
- **"Post Coffee Walk" appears SIX times as separate, identically-named options** in the same
  "Cardiovascular Activities" category of the Log Activity picker. Not confirmed as a bug (could be
  intentional per-tenant/per-campaign custom activity entries with the same display name but different
  underlying IDs) — flagged as a Note/Doubt worth a follow-up DOM inspection to check if they're
  genuinely distinct entries or a duplicate-rendering defect.
- **No "Running", "Jogging", "Cycling", or generic "Walking" activity type exists** in the Log Activity
  picker at all, despite the Distance card having dedicated "Jog / Run" and "Cycling" rows. **Resolved,
  not an open question:** the Quick Add menu's own "Start Outdoor Workout — Track on app" entry
  confirms these categories are populated exclusively via app-based GPS tracking — see the corrected
  Distance-card entry above (formerly "Bug #4").
- **Keyboard/accessibility findings from a full-page Tab sweep (desktop, 1440×900):**
  - The first Tab stop is the header logo/"Home" link — **no "Skip to main content" link exists**,
    confirming `DIARY-A11Y-TC-013`'s suspected gap as real.
  - **No visible focus indicator** on any element checked (Home link, Redeem link) — `outline-style:
    none`, `box-shadow: none`, no border change. Confirmed via `getComputedStyle` on two separate
    focused elements, not a one-off. This is a real, systemic finding for `DIARY-A11Y-TC-007`, not a
    nitpick — keyboard-only users have no visual indication of focus position anywhere checked.
  - **The "More" nav overflow button (`class="more-trigger"`, kebab/ellipsis icon) has NO accessible
    name** — empty `aria-label`, empty text content — yet is keyboard-focusable. A screen-reader user
    tabbing to it would hear only "button," with no indication of its function.
  - **Heading hierarchy skips a level in the footer**: the page's main content correctly uses a single
    `<h1>` ("Diary") followed by consistent `<h2>`s for every card (Snapshot, Calorie Ledger, Food Log,
    Sleep, Intake, Distance, Activities, Vitals) — clean. But the footer's tagline ("Sweat now, Shine
    later.") is itself marked up as an `<h2>`, followed directly by `<h4>` ("Powered by Vantage Circle"),
    skipping `<h3>` entirely. A real, confirmed finding for `DIARY-A11Y-TC-002`.
  - **Tab order for the date-nav controls is correct** — confirmed via DOM query that "Previous day"
    (focusable index 17) precedes the Snapshot card wrapper (index 18), matching visual top-to-bottom
    order; initial manual spot-checks suggested an anomaly but a full DOM-order query resolved it as a
    counting error, not a real gap.
  - **Previous day is keyboard-operable**: focused programmatically and activated via a real `Enter`
    keypress, correctly navigated back one day (17→16 July) — confirms `DIARY-HDR-TC-024` at least for
    Previous day; Next day was not independently re-tested via keyboard this pass (same component
    pattern, reasonable to expect symmetric behavior, but not confirmed with equal rigor).

---
## 2026-07-17 — Deep root-cause investigation of Weight inconsistency (VIT-TC-007/011)

## Bug #5 [Enhancement - P4]
[Diary → Vitals card — Weight display disagrees with Summary and the Log Weight modal because two
backend endpoints implement two different query semantics for "current weight"]
Root-caused at the API level, not assumed. Three surfaces, three different backing calls, captured on
the same account on the same day (17 July, no new weight log that day):
- **Diary's Vitals card** ← `POST /vantagefit/api/v1/today/overview` → returned
  `{"dataType":"Weight","value":"--","rawValue":null}`.
- **Log Weight modal's pre-fill** ← a fresh `GET /vantagefit/api/v1/app/home` call, triggered the
  moment the modal opens → returned `{"key":"WEIGHT","value":"164.91","unit":"lbs","subtitle":"Updated
  on 16 Jul 2026"}`.
- **Summary's Vitals card** ← the same `GET /vantagefit/api/v1/app/home` endpoint → byte-identical
  payload to the modal's.
Confirmed this is NOT a "backend fails to send data" bug: on 14 July, a day with a real same-day weight
log, `today/overview` correctly returns the value (`"Weight":"164.4 lbs","rawValue":74571`). So the
endpoint can return weight — it simply implements a strict **"was there a log tied to this exact
calendar date"** query, with no fallback to a prior value. `app/home`, by contrast, implements
**"what's the most recent known value as of now," with an explicit "Updated on <date>" label** so the
UI can be transparent about staleness.
Root cause: two backend endpoints encode two legitimately different (and both internally consistent)
definitions of "current weight." Diary's own page pulls from both — its card display uses the
strict-per-date endpoint, its own edit modal uses the always-fallback endpoint — so even one page
contradicts itself, independent of the Summary-vs-Diary angle.
Reclassified from a P3 bug to an **Enhancement**: nothing is broken, lost, or mis-transmitted; this is
a product decision about which semantic a bare "Weight" label (with no date context of its own) should
use, consistently, everywhere. Two reasonable fixes: (a) make `today/overview`'s Weight field fall back
to most-recent-known-value the same way `app/home` does, or (b) if "blank unless logged today" is
intentional, the card should say something like "No log today" rather than a bare "--" that reads as
"never recorded," since the app demonstrably knows otherwise.
Evidence: raw response bodies for both endpoints captured via `browser_network_request` this session;
see `dashboard/diary/test-cases/diary.md` DIARY-VIT-TC-007/011 for the original observations this
explains.

## Bug #6 [UI - P3]
[Diary → Vitals card — the "Edit" icon for Mood/Weight renders as a blank circle because its SVG icon
has no path data (missing icon asset), not because it's merely ambiguous]
Before logging, Mood/Weight/Heart Rate all show a `+` icon button ("Log X"). After logging Mood or
Weight, the button correctly relabels to "Edit mood"/"Edit weight" (confirmed via accessible name — the
underlying functionality and semantics are correct) but the icon inside it renders as a fully blank
circle, with nothing visible at all — not a pencil, not any icon.
Inspected the DOM directly to find the exact cause. The working `+` icon:
`<fit-icon><svg ...><path d="M5 12h14"></path><path d="M12 5v14"></path></svg></fit-icon>` — two real
path elements drawing a plus sign. The "Edit mood"/"Edit weight" icon, by contrast:
`<fit-icon><svg ...></svg></fit-icon>` — the exact same well-formed, correctly-styled, correctly-sized,
fully-visible (`opacity:1`, `visibility:visible`, 12×12px, correct stroke color) SVG container, but with
**zero child path/shape elements inside it**. Confirmed identical (same empty pattern) for both "Edit
mood" and "Edit weight" — this is systemic, not a one-off render glitch.
Expected: a clear edit affordance (e.g. a pencil icon), consistent with the icon actually rendering.
Actual: an empty circle. A user has no visual cue that this control is even interactive/different from
decoration — it looks like a loading placeholder or a broken icon reference, exactly as originally
reported.
Root cause: the icon component (`<fit-icon>`) is very likely being passed an icon name for the "edit"
state that doesn't resolve to any registered icon (a missing or misspelled icon identifier), so it
renders its empty SVG shell instead of failing loudly. The `+`/add-state icon resolves correctly because
its name is presumably valid.
Not affected: Heart Rate (stays app-only, never reaches this button state on web); Sleep (has no edit
affordance at all — see Bug #2, a different, more severe gap); Water/Food Log (their add buttons are
text buttons, not icon-only, so this specific empty-icon failure mode doesn't apply to them, though they
have their own separately-tracked issues).
Evidence: `dashboard/diary/evidence/icon_vitals_before_log.png` (before — clear "+"),
`dashboard/diary/evidence/icon_vitals_after_log.png` and `bug_edit_icon_missing.png` (after — blank
circles on Mood and Weight), plus the raw `outerHTML` comparison captured via `browser_evaluate` this
session.

---

## Section F: Intake card (Water/Calories) — P2 pass findings, 2026-07-17

## Bug #7 [Enhancement / Copy - P3]  — REVISED 2026-07-17 after QA-lead manual retest
[Diary → Log Water modal — the "of 8 glasses" daily target is never explained as an app-set default
minimum, so exceeding it reads as the confusing "N of 8 glasses"]

**Correction to the original filing:** the original claim that the counter "has no upper bound" was
WRONG. Manual retest by the QA lead, independently re-confirmed live this run, shows a real hard cap
of **5 L / day** — the "Add a glass" button disables at exactly 20 glasses (= 5000 ml). The "8" is
the app's **default daily MINIMUM target** auto-assigned to every user, not a hard limit. So
"9 of 8 glasses" / "20 of 8 glasses" is technically internally consistent (N logged against a
target of 8) — the real issue is a UX/copy gap, not broken logic or a missing cap.

The real gap: the user is never told that "8" is an app-determined default minimum target. With no
label or benchmark explaining it, "20 of 8 glasses" reads as nonsensical and the "8" looks
arbitrary.

Expected: the "of 8" should be visibly framed as a default / recommended minimum daily goal — e.g.
"of 8 (daily goal)", a target/benchmark marker on the slider, or a one-line note that 8 is a
recommended minimum. The existing "Daily goal reached" copy is good; it's the bare "N of 8"
phrasing that needs softening for the over-target state.

Assessment (QA): **copy/UX enhancement, not a functional defect.** Reclassified from the original
P2-Functional down to **P3-Enhancement**. The hard cap (5 L) and the arithmetic both work correctly.
Evidence: reconfirmed live this run — "Add a glass" disabled at 20 glasses / 5000 ml (see
DIARY-INTAKE-TC-011).

## Bug #15 [Functional / UI - P1]  — new, QA-lead manual find + live corroboration 2026-07-17
[Diary → Log Water modal — unit-bearing labels do not fully convert when the unit toggle is switched
to fl oz; some stay in ml / L]
Switching the modal's unit toggle from "ml" to "fl oz" correctly converts the PRIMARY value
(5000 ml → 169 fl oz) and the slider tick scale (0–5000 → 0–160). But not every unit-bearing label
follows:
- **Confirmed live this run:** the "1 glass = 250 ml" helper text stays in **ml** in fl oz mode
  (should read the fl-oz equivalent, ~"1 glass = 8.45 fl oz").
- **Reported by the QA lead via manual test:** a "daily max is 5L" message shown while dragging the
  slider likewise stays in **L**, not converted to fl oz.
Expected: switching the unit converts ALL displayed values AND labels consistently — no residual
ml / L text left behind in fl oz mode.
Actual: partial conversion — primary value + slider convert, but secondary helper/warning labels
("1 glass = 250 ml", "daily max is 5L") remain in metric.
Why P1 (per QA-lead direction): this is the same unit-mismatch family as BUG-01 (Water shown as
"25.36 / 2.5 L" — an fl-oz numerator against an "L" label). Mixed units on a data-entry surface risk
the user logging the wrong amount — a data-integrity concern, not merely cosmetic.
Note/Doubt: the "1 glass = 250 ml" persistence was confirmed live via DOM read this run. The
specific "daily max is 5L" drag-warning was NOT reproduced via automation — the slider is a custom
drag track with no standard `input[type=range]`/`role="slider"` to script, so that exact string
rests on the QA lead's manual observation, corroborated by the confirmed "250 ml" instance of the
same defect class. **General testing heuristic to carry forward: whenever a modal has a unit toggle
(ml/fl oz, kg/lbs, km/mile), verify EVERY value AND label converts, not just the headline number.**
Evidence: `browser_evaluate` DOM read of the fl-oz-mode modal this run; QA-lead manual observation
for the drag-warning.

## ~~Bug #8~~ → MOVED to the "Keyboard / Focus-management findings (PARKED)" section at the end of
this file, per QA-lead direction (2026-07-17): keyboard-focus issues are parked and reproduced only
on request. See PARKED-KF-1 below.

---

## Section G: Distance card — P2 pass findings, 2026-07-17

## ~~Bug #9~~ — WITHDRAWN 2026-07-17 (QA-lead direction, agreed)
[Was: Distance card empty rows ("—") have no accessible equivalent.]
Withdrawn. The "—" simply reflects that there is no distance data for that row, and a bare dash for
a genuinely-absent metric is an accepted empty-state convention that both sighted and screen-reader
users reasonably understand. Senior QA take: flagging this as an accessibility defect was
over-zealous — it's a nitpick at most, not a defect. Not a bug; removed from the active list.

---

## Section I: Vitals card — P2 pass findings, 2026-07-17

## ~~Bug #10~~ → MOVED to the "Keyboard / Focus-management findings (PARKED)" section at the end of
this file, per QA-lead direction (2026-07-17): keyboard-focus issues are parked and reproduced only
on request. See PARKED-KF-2 below.

---

## Section J: Footer/Global Chrome — P2 pass findings, 2026-07-17

**Cross-reference, not a new bug:** confirmed the known chat-widget/card-overlap pattern already
tracked on Summary (Bugs #24/#28/#45) also reproduces on Diary. At 390×844, scrolled so the Vitals
card's Weight row sits at the bottom of the viewport, its rect (top 825, bottom 861) genuinely
overlaps the floating "Chat with us" button's fixed rect (top 779, bottom 829, left 15, right 160)
by ~4px, confirmed both by bounding-rect measurement and a screenshot showing the row partially
obscured. See DIARY-CHROME-TC-014. Recommend broadening the existing Summary bug's scope to note
it affects Diary too, rather than treating this as page-specific.

## ~~Bug #11~~ — IGNORED 2026-07-17 (QA-lead direction)
[Was: active nav tab indicated visually only, no `aria-current`.] Set aside per QA-lead direction —
not being pursued this cycle. Retained here only as a record; not part of the active/reported list.

## ~~Bug #12~~ — IGNORED 2026-07-17 (QA-lead direction)
[Was: Freshchat "Chat with us" widget doesn't close on Escape.] Set aside per QA-lead direction (and
it's a third-party widget outside the product's own codebase anyway) — not being pursued this cycle.
Retained here only as a record; not part of the active/reported list.

---

## Section L: Responsiveness — P2 pass findings, 2026-07-17

## Bug #13 [UI - P2]
[Diary, mobile landscape (844×390) — the floating Chat/Quick-Add pill bar significantly overlaps
the Snapshot card, worse than the already-known portrait-mode overlap (Bugs #24/#28/#45)]
At 844×390 (a phone rotated to landscape), the main content grid correctly adapts to a
desktop-style multi-column layout (full pill nav, "+ Add" button visible). However, the floating
bottom pill bar ("Chat with us" / home icon / "+" / gift icon) stays fixed in its mobile-style
bottom-left position and does not adapt. Measured via bounding rects: the Snapshot card spans
y[201,620] x[21,414]; the floating pill bar spans y[325,375] x[15,160] — a genuine ~50px-tall
overlap zone squarely inside the Snapshot card, confirmed visually in a screenshot showing the
pill bar covering part of the ring-chart/Steps area.
Expected: the floating widget/nav bar should reposition, shrink, or hide at this width/orientation
to avoid covering card content — consistent with how the rest of the layout correctly adapts to
this breakpoint.
Actual: only the main content grid adapts; the floating bar does not, and the overlap here (~50px)
is substantially larger than the ~4px overlap already documented in portrait mode (see the
Section J/TC-014 cross-reference above, and Summary's Bugs #24/#28/#45).
Note/Doubt: same root cause/systemic pattern as the existing bugs — recommending this be folded
into that same tracked issue's scope (add "landscape mobile, more severe" as a data point) rather
than treated as fully separate, though filing it distinctly here since the magnitude and specific
trigger condition (landscape orientation) are new information.
Evidence: reproduced live via `browser_resize` + `browser_evaluate` bounding-rect measurement and
a screenshot this session (see DIARY-RESP-TC-009/011).

---

## Section M: Global accessibility — P2 pass findings, 2026-07-17

## Bug #14 [Accessibility - P2]
[Diary — saving any entry (Mood/Weight/Sleep/Water) produces no confirmation of any kind, visual
or announced]
Re-saved Mood (clicked "Edit mood" → selected the same value → "Update") and immediately queried
the DOM for `[aria-live]`, `[role="status"]`, `[role="alert"]`, and any toast/snackbar-styled
element — found zero matches, both before and after the save.
Expected: some form of success confirmation — even a brief toast — that is also exposed via
`aria-live` so screen-reader users are told the save succeeded, not just sighted users inferring
it from the modal closing and the card updating.
Actual: completely silent. The modal closes and the underlying card updates in place, but there is
no toast, snackbar, or live-region announcement anywhere in the DOM at any point during or after
the save.
Note/Doubt: this is a UX/accessibility gap rather than a functional break — the save itself works
correctly every time (confirmed extensively throughout this session). Framing this as "add a
confirmation," not "fix a broken save."
Evidence: reproduced via `browser_evaluate` DOM query immediately after a live save this session
(see DIARY-A11Y-TC-006); no screenshot (the finding is the ABSENCE of any visual/DOM element).

---

## P3/P4 cross-cutting pass (visual / copy / design-system) — 2026-07-17

## Bug #16 [UI / Design-system - P3]
[Diary — empty states are inconsistent across the page's cards; at least five different conventions
for "no data" on one screen]
A full-page review (plus empty-day observations from the cross-date pass) shows each card handles
its no-data state differently:
- **Food Log:** full sentence "No food logged for this day." + centered "Log meals" button.
- **Activities:** full sentence "No activities logged." + button.
- **Sleep (empty day):** a two-line block — "No Data" heading + "Track your sleep to see insights"
  subtitle + button.
- **Distance:** a bare em-dash "—" per row (Moved / Jog-Run / Cycling).
- **Vitals:** a double-dash "--" per row (Mood / Heart Rate / Weight).
- **Intake:** "0 / 2.5 L" and "0 kcal" (a real zero value, not an empty-state treatment).
Expected: a consistent empty-state pattern across cards of the same page — either all worded, all
dash, or a documented design-system rule for when each applies (e.g. "list cards get a sentence,
metric rows get a dash"). Even the dash itself differs ("—" em-dash vs "--" double-hyphen).
Actual: five different treatments, including two different dash glyphs, with no obvious rule.
Assessment: design-system consistency gap — the primary target of this validation per the project
brief. P3 (polish/consistency, not functional). Maps to DIARY-VIT-TC-019 and the per-card
empty-state rows.
Evidence: `p3p4_fullpage_desktop.png` (Today — shows Food Log sentence vs Distance/Vitals dashes
side by side) plus the 30 June empty-day observations from the cross-date pass (Sleep "No Data",
Activities "No activities logged").

Note on contrast checks (DIARY-*-A11Y contrast rows): the app defines most text colors in the
**oklch/oklab** color space. Automated contrast sampling this pass could only reliably compute
plain-`rgb()` colors (e.g. the Distance "mile" label, gray-500 on white ≈ 4.83:1, a pass). oklch/
oklab-based text could not be reliably converted to a WCAG ratio with the inline tooling available,
so those contrast rows are marked "not reliably measured — needs a real axe/contrast tool" rather
than given a false pass/fail. No obvious low-contrast text was visually apparent on the cards.

---

## Keyboard / Focus-management findings (PARKED — reproduce only on request)

Per QA-lead direction (2026-07-17), keyboard-focus / focus-trap issues are parked in this separate
section and are NOT part of the active reported bug list. They are recorded (not deleted) so the
detail isn't lost, and will be re-verified/reported only when explicitly asked. Priorities shown are
provisional and not carried into the main severity counts.

### PARKED-KF-1 [Accessibility] — Log Water AND Log Weight modals do not trap keyboard focus
(Formerly Bug #8.) With the Log Water modal open, focus on the "Log water" submit button, one more
Tab moved `document.activeElement` to the page footer's "FAQ" link (`closest('[role="dialog"]')` =
false) — focus left the modal while it stayed visually open. The identical escape pattern was then
reproduced in the Log Weight modal (focus on "Update weight" → Tab → footer "FAQ"). So it affects at
least two data-entry modals. Contrast: the Sleep modal (DIARY-SLEEP-TC-020) correctly traps focus.
Not the same as the Calorie Ledger "Learn more" hand-off popover (decorative). Only tested from the
submit button forward; full in-modal tab order not enumerated. Evidence: live
`browser_press_key`/`browser_evaluate` this session (DIARY-INTAKE-TC-026, DIARY-VIT-TC-027).

### PARKED-KF-2 [Accessibility] — Log Mood modal: keyboard focus never enters the modal at all
(Formerly Bug #10.) Opening the Log Mood modal ("Edit mood") left `document.activeElement` on the
trigger button (`closest('[role="dialog"]')` = false); Tab then cycled through background page
buttons, never landing inside the dialog. A keyboard-only user cannot reach the mood options, tag
buttons, or "Update" by tabbing — a more severe variant than PARKED-KF-1 (there focus starts inside
and leaks at the end; here it never enters). Shift+Tab / screen-reader virtual-cursor routes not
tested. Evidence: live `browser_press_key`/`browser_evaluate` this session (DIARY-VIT-TC-027).
