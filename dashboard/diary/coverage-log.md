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

## 2026-07-16/17 — Smoke suite execution (dashboard/smoke/diary-smoke.md, 19 cases)
- **Status:** ✅ Done — 17 Pass, 1 Fail, 1 Pass-with-flagged-Note/Doubt
- Live-executed the full Diary smoke suite via Playwright against production, across a session that
  genuinely spanned a real midnight rollover (16→17 July) and one mid-session re-login after the
  browser session dropped.
- **1 new bug filed** (Bug #2: no edit affordance for an already-logged Sleep entry from Diary).
- **1 existing bug materially re-assessed**: Bug #1 (Water) — a fresh, controlled re-test (logging a
  known 750ml amount and inspecting the raw API) directly contradicted the original "cross-day
  misattribution" diagnosis; the entry was correctly and freshly attributed to Today. What remains
  confirmed live: a narrower fl-oz/L unit-label mismatch and a modal-goal-vs-card-goal (2L vs. 2.5L)
  mismatch. See bug-log.md for the full write-up — this is a significant correction to a previously
  P1-filed defect, not a minor addendum.
- Positive/confirmed-working: page load, header/date accuracy across two real days, Previous/Next day
  navigation (including a 3-day round trip with exact data restoration), Snapshot cross-page
  consistency, Sleep/Weight/Mood logging and editing (all correctly update in place and persist),
  Activities counter accuracy, Calorie Ledger arithmetic (verified exact across 5 different days),
  mobile layout (390×844, no overflow, sticky header confirmed), zero real application console errors.
- New items surfaced for future test-case coverage (not yet reflected in `diary.md`): Weight modal
  pre-fills a "last known value" the card itself doesn't surface (direct evidence for Section I's
  open TC-016 question); Mood's positive-response flow has an undocumented "what made it good?" tag
  picker; Freshchat chat widget renders 0×0 on mobile (needs product confirmation, not asserted as a
  bug); the page's real scroll container is Angular Material's `.mat-drawer-content`, not
  `document.body` (automation note).
- Not yet executed: the 291-case `dashboard/diary/test-cases/diary.md` main suite (Sections A–M) —
  smoke suite was completed first per agreed sequencing.

## 2026-07-17 — P1-priority pass across diary.md main suite (~84 P1 cases, all 13 sections)
- **Status:** ✅ Done (P1 only — P2–P4 deferred to a later pass, per agreed sequencing)
- Live-executed every P1-priority case across Sections A–M, reusing smoke-suite evidence where it
  directly overlapped and gathering fresh evidence for genuinely new checks (accessibility DOM audits,
  Distance-card hypothesis resolution, Activities in-place-update test, Sleep/Weight persistence via a
  session-interruption round trip).
- **2 new bugs filed:**
  - Bug #3 (P1): Activities card does not update in place after logging a new activity — requires a
    hard reload, same root-cause family as Summary's known Bug #2. Also confirmed the Snapshot card
    shares this exact gap (Steps/Active Minutes also required a reload after the same log action).
  - ~~Bug #4~~ **Retracted after correction:** originally filed as "Distance card doesn't reflect a
    manually-logged activity's distance, pending product confirmation." Corrected once the Quick Add
    menu's own "Start Outdoor Workout — Track on app" entry was properly accounted for — this is the
    same app-only convention already established for Heart Rate, and is almost certainly the real
    mechanism behind Distance's Jog-Run/Cycling/Moved rows. "Log Activity" (manual entry, used in the
    original test) was simply the wrong mechanism to test against. Not a bug — resolves Section G's
    open hypothesis (DIST-TC-001–003) as an app-only limitation. See bug-log.md for the full correction.
- **4 confirmed accessibility findings** (DOM-verified, not speculative): no visible focus indicator
  on any checked element (`DIARY-A11Y-TC-007`, confirmed on 2 separate elements); the "More" nav
  overflow button has zero accessible name; no "Skip to main content" link exists
  (`DIARY-A11Y-TC-013`); the footer skips a heading level (H2→H4, no H3) (`DIARY-A11Y-TC-002`).
- **1 existing bug materially corrected:** Bug #1 (Water) — see the smoke suite's entry above; the
  original "cross-day misattribution" diagnosis did not survive a controlled re-test. The confirmed,
  still-live issue is narrower (fl-oz/L unit-label mismatch, `DIARY-INTAKE-TC-006`).
- **New cross-page/card-vs-modal finding:** Diary's Vitals Weight card shows "--" for a day with no
  new log, while Summary's Vitals card and the Log Weight modal itself both retain/surface the last
  real value — three different representations of "current weight" across the app
  (`DIARY-VIT-TC-007/011`).
- **Positive/confirmed-working (P1 scope):** header/date accuracy, all date-navigation mechanics,
  Snapshot/Calorie Ledger/Sleep/Vitals data accuracy and cross-day scoping, Water's core
  misattribution-free behavior, Sleep/Weight/Mood write-paths (log + edit, all persisting through a
  full session interruption), Activities row-level accuracy and counter, keyboard operability of
  Previous-day (Enter-key activation confirmed), correct tab order for the date-nav→Snapshot
  transition, primary-mobile-viewport layout (390×844, no overflow).
- **Genuine gaps left for a follow-up pass** (not fabricated, explicitly marked "not yet executed" in
  diary.md): non-sequential date-jump testing (XDATE-TC-002); multiple-same-day-activity ordering
  (ACT-TC-004); Sleep's overnight-boundary and end-before-start edge cases (SLEEP-TC-004/012); Weight's
  unrealistic-value and past-day-attribution checks (VIT-TC-014/015); most of Section L's deeper
  mobile checks (modal-at-mobile-width, on-screen-keyboard, iPhone SE 375×667); Section M's per-modal
  label-association and validation-announcement audits (A11Y-TC-004/005); cross-platform Heart Rate
  sync (VIT-TC-018, needs Android coordination).
- **Not yet executed:** P2–P4 priority cases across all sections (~207 remaining rows), and the
  Section J (Footer/Global Chrome) sweep — that section has zero P1-priority cases by design, so it
  was untouched this pass.

## 2026-07-17 — Deep root-cause investigation: Weight inconsistency (VIT-TC-007/011) + new icon finding
- **Status:** ✅ Done — user-requested deep-dive on the previously-flagged Weight discrepancy
- Traced the exact backing API for all three surfaces via `browser_network_request`: Diary's Vitals
  card (`today/overview`), the Log Weight modal's pre-fill (`app/home`, fresh call confirmed on modal
  open), and Summary's Vitals card (`app/home`, same endpoint). Confirmed the backend is not failing to
  send data — `today/overview` correctly returns a weight value on days with a genuine same-day log
  (14 July). The two endpoints simply implement different semantics: strict same-date-only vs.
  most-recent-known-value.
- **Reclassified VIT-TC-007/011 from Bug (P1) to Enhancement (Bug #5, P4)** per user direction — this is
  a product/architecture decision, not a functional defect.
- **New bug found during the same investigation, at the user's explicit request:** the "Edit
  mood"/"Edit weight" buttons that appear after logging Mood/Weight render a completely blank icon
  (empty circle) instead of a pencil/edit affordance. DOM inspection found the SVG icon container is
  well-formed and visible but contains zero path data — a missing/misresolved icon asset, confirmed
  identical for both Mood and Weight. Filed as **Bug #6 (P3, UI)**; new test case DIARY-VIT-TC-029
  added to `diary.md`.
- Confirmed NOT affected by the same icon issue: Heart Rate (never reaches this button state on web),
  Sleep (no edit affordance at all, per the already-filed Bug #2), Water/Food Log (text buttons, not
  icon-only, so this specific failure mode doesn't apply).

## 2026-07-17 — P2-priority pass, Sections E–F (Sleep, Intake)
- **Status:** ✅ Done for E (Sleep) and F (Intake); continuing section-by-section per "P2 only, skip
  P3/P4" direction
- Section E: live-tested the Log Sleep modal's stepper precision (confirmed exact non-round durations,
  no forced rounding), confirmed the modal correctly traps keyboard focus (positive contrast to the
  Calorie Ledger "Learn more" modal), confirmed the "Add Sleep Data" button's accessible name. Marked
  SLEEP-TC-008/009/010/014 as not-yet-executed (would need slider-drag or double-submit tests not
  attempted this pass).
- Section F: live-tested the Log Water modal's glass stepper (exact +1/−1 per click, correct ml
  arithmetic, cannot go negative), reconfirmed the goal-value mismatch (2.5L card vs 2000ml modal,
  same defect as Bug #2), confirmed "0kcal" displays correctly with no meals logged.
- **Two new P2 findings from live testing, not previously filed:**
  - **Bug #7 (P2, Functional):** the glass stepper has no upper bound tied to the stated goal —
    clicking past 8 glasses produces the literal (and previously only anecdotally-seen) "9 of 8
    glasses / Daily goal reached" string.
  - **Bug #8 (P2, Accessibility):** the Log Water modal does NOT trap keyboard focus — Tab from the
    submit button escaped to the page footer's "FAQ" link. This contradicts the test case's original
    assumption (based on Sleep's modal, which does trap correctly) that all of Diary's real modals
    behave the same way — they don't; this is modal-specific.
- No data was left behind: the 9-glass test session was closed via "Close" without saving, and the
  Intake card was confirmed to still read "0/2.5 L" afterward.

## 2026-07-17 — P2-priority pass, Sections G–H (Distance, Activities)
- **Status:** ✅ Done
- Section G: reconfirmed the Distance card correctly stays empty for non-matching activity types
  (Hiking, today), and found a new minor accessibility gap — the empty "—" rows have no accessible
  equivalent (no aria-label, no visually-hidden text). Filed as **Bug #9 (P3, Accessibility)**.
- Section H: live-logged a second activity (Yoga) via Quick Add to test icon rendering and duration
  handling. Found the Duration stepper has a **5-minute floor** — a genuinely 1-minute activity
  cannot be logged at all via this control (DIARY-ACT-TC-010, partial). Confirmed 2 distinct,
  correct activity icons (hiking.png, yoga.png). Re-confirmed the "View all" button (which only
  appears once activities exceed the card's 3-row visible limit — found by checking 14 July's
  7-activity day) still opens the add-activity picker instead of a full list — this reproduces an
  already-known bug from the quick-add area, not filed as new. Confirmed clicking a logged row does
  nothing (known gap, not new).
- Both new activities (Hiking, Yoga) remain on Today's diary as live test data going forward (both
  logged on the UAT-safe tenant); no cleanup performed as none was required.
- Next: continuing the P2 pass into Section I (Vitals), then J onward, per standing instruction.

## 2026-07-17 — P2-priority pass, Section I (Vitals)
- **Status:** ✅ Done
- Reconfirmed kg↔lbs conversion accuracy (74.8kg = 164.91lbs) and decimal precision (0.1kg stepper
  granularity, "0.1 kg below last log" label). Documented the Weight card's same-day-only display
  model explicitly (via the existing Bug #5 trace) as the answer to "does it carry forward" — it
  does not, by design, on this specific card.
- Confirmed Heart Rate's app-only pattern holds consistently. Confirmed Mood empty-state evidence
  already gathered earlier this session satisfies TC-001. Confirmed Mood option buttons all expose
  real text accessible names (not icon-only).
- **Two focus-trap findings from live keyboard testing:**
  - Reproduced the Log Water modal's focus leak (Bug #8) in the **Log Weight modal** too — same
    escape pattern from the submit button to the footer's "FAQ" link. Updated Bug #8's title/scope
    rather than duplicating it.
  - Found a **distinct, more severe** gap in the **Log Mood modal**: opening it does not move
    keyboard focus into the dialog at all — Tab cycles through background page buttons, never
    reaching any control inside the modal. Filed as new **Bug #10 (P2, Accessibility)**.
- Mood/Weight modal states were both closed without saving after testing; Vitals card confirmed
  unchanged (Mood "Okay", Weight "164.91 lbs") afterward.
- Next: continuing the P2 pass into Section J (Footer/Global Chrome), then K onward.

## 2026-07-17 — P2-priority pass, Section J (Footer/Global Chrome)
- **Status:** ✅ Done
- Resolved the open question from TC-002: the "Summary" tab correctly shows as visually active
  while viewing Diary. However, found it's **visual-only** — no `aria-current` on any nav tab.
  Filed as **Bug #11 (P3, Accessibility)**, scoped as a shared nav-component issue (affects every
  page using this nav, not just Diary).
- Confirmed the Chat widget opens/closes correctly via its own buttons, but **Escape does not
  close it** — filed as **Bug #12 (P4, Accessibility)**, explicitly noted as a third-party
  (Freshchat) limitation, not necessarily fixable in Vantage Fit's own codebase.
- **Reproduced the known Summary chat-widget/card-overlap pattern (Bugs #24/#28/#45) on Diary
  too** — confirmed via bounding-rect measurement and a screenshot that the floating chat button
  overlaps the Vitals card's Weight row by ~4px at certain scroll positions on a 390×844 viewport.
  Not filed as a new bug; recommended broadening the existing bug's scope instead.
- Confirmed all footer links have real, well-formed hrefs and accessible names (not verified by
  live navigation to each destination, to avoid repeatedly leaving the Diary session).
- "Go back to the Top" scroll-to-top works correctly; focus stays on the (still on-screen, fixed)
  button rather than moving to top content — logged as a minor partial finding, not a hard fail.
- Next: continuing the P2 pass into Section K (Cross-date regression), then L onward.

## 2026-07-17 — P2-priority pass, Section K (Cross-date regression)
- **Status:** ✅ Done
- The month-boundary case (17 clicks back to 30 June) was actually reachable and executed live —
  full 9-card sweep confirmed clean, correct empty states with no cross-day contamination. Notably
  surfaced a genuine "No Data / Track your sleep to see insights" Sleep empty state not seen on any
  previously-visited day (all prior days checked had at least some sleep data).
- Year-boundary (~198 clicks) and full 1-year-back (~365 clicks) cases remain not practically
  executable via sequential-only navigation, consistent with the same constraint already documented
  for Section A's HDR-TC-008/010 — no jump-to-date feature exists.
- Next: continuing the P2 pass into Section L (Responsiveness), then M.

## 2026-07-17 — P2-priority pass, Section L (Responsiveness)
- **Status:** ✅ Done (2 rows genuinely not testable via this automation setup, honestly marked)
- Confirmed clean layouts via live screenshots at 360×800, 768×1024 (iPad portrait), 1024×768
  (iPad landscape), and 1366×768 (laptop) — all render sensible, non-cramped, non-overflowing
  layouts appropriate to each width.
- **Found a new, more severe variant of the known chat-widget overlap** at mobile landscape
  (844×390): the floating pill bar overlaps the Snapshot card by ~50px (vs. ~4px in portrait,
  Section J). Filed as **Bug #13 (P2, UI)**, cross-referenced to the existing Bugs #24/#28/#45.
- Confirmed touch-target SPACING between Log Water's Add/Remove glass buttons is sufficient
  (~54px apart); separately noted each button's own tappable area (34×34px) is below the 44×44px
  guideline — a distinct, smaller-scope observation from what this case asked.
- Confirmed the app's CSS does reference `safe-area-inset`, but true on-device verification isn't
  possible from this environment.
- **Two rows marked honestly not-testable rather than faked:** TC-022/026 (125–400% browser zoom)
  — attempted via Chromium's `style.zoom` CSS hack, but confirmed this does NOT shrink
  `window.innerWidth` the way real browser zoom does, so it can't trigger genuine responsive
  reflow. TC-018 (macOS-specific rendering) — only one Chromium engine is available in this
  environment; a real cross-OS comparison needs manual testing on actual Windows and macOS.
- Next: continuing the P2 pass into Section M (Global accessibility) — the final section.

## 2026-07-17 — P2-priority pass, Section M (Global accessibility) — PASS COMPLETE
- **Status:** ✅ Done — this closes out the full P2-priority pass across all 13 sections (A–M)
- Confirmed main-content heading hierarchy is clean (single H1, consistent H2 cards); reconfirmed
  the pre-existing footer H3-skip still reproduces (already tracked, BUG-09 in the shared report).
- Confirmed all standard landmark regions exist (header/nav/main/footer).
- **New finding:** saving any entry (tested via Mood) produces zero confirmation of any kind —
  no toast, no `aria-live` region, nothing. Filed as **Bug #14 (P2, Accessibility)**.
- TC-009/010 (reduced-motion, forced-colors emulation) marked honestly not-testable — both require
  Playwright's CDP-level media emulation, not exposed via this session's generic browser tools;
  confirmed via a stylesheet scan that the CSS does contain both `@media` rules, a positive but
  incomplete signal.
- TC-011 (full read-through coherence) passed via the accessibility-tree-as-proxy method the case
  itself allows, cross-referencing already-tracked Bugs #6/#9 for the specific gaps found earlier.

## P2 pass summary (all sections A–M)
- **New bugs filed this pass:** #7 (glass-stepper overage copy), #8 (Water/Weight modals don't trap
  focus — updated in place), #9 (Distance empty-state no accessible equivalent), #10 (Mood modal
  focus never enters at all), #11 (no `aria-current` on active nav tab), #12 (Freshchat Escape
  doesn't close), #13 (landscape mobile chat-widget overlap, more severe variant), #14 (silent save,
  no confirmation of any kind).
- **Known-bug reproductions confirmed, not re-filed:** Activities' "View all" mislabeled behavior
  (quick-add area's known bug), the Summary chat-widget/card-overlap pattern (Bugs #24/#28/#45) —
  now confirmed on Diary too, in both portrait and (worse) landscape.
- **Honestly marked not-testable, with reasons documented rather than guessed:** browser zoom
  125–400% (TC-022/026 — automation's zoom simulation doesn't replicate real zoom), true
  Windows-vs-macOS rendering comparison (TC-018 — single browser engine available), QR code
  payload decoding (TC-008 — no decode tool available), safe-area on a real notched device
  (TC-010 — env() returns 0 outside a real device), reduced-motion/forced-colors runtime behavior
  (TC-009/M-010 — no CDP media-emulation tool exposed).
- **Deferred per explicit user instruction:** all P3/P4 rows across every section remain
  unexecuted, to be picked up in a future pass if requested.

## 2026-07-17 — QA-lead manual retest + bug reclassification (corrections to the P2-pass findings)
- **Status:** ✅ Done — applied the QA lead's manual-retest feedback across bug-log.md and diary.md.
- **Bug #7 revised** (was P2-Functional "no upper bound / nonsensical 9-of-8") → **P3 Enhancement.**
  Manual + live re-test confirmed a real 5 L/day hard cap (Add-a-glass disables at 20 glasses); the
  "8" is an app-set default daily *minimum* target. Real gap is that the default-target is never
  explained — needs a label/benchmark, not a logic fix.
- **New Bug #15 (P1)** — Log Water unit toggle only partially converts: switching ml→fl oz converts
  the goal (5000 ml→169 fl oz) + slider ticks, but "1 glass = 250 ml" stays ml (confirmed live) and
  a "daily max is 5L" drag-warning stays L (QA-lead manual obs). Same unit-mismatch family as
  BUG-01. New test case DIARY-INTAKE-TC-027 added. Saved as a reusable testing heuristic to memory.
- **Bug #9 withdrawn** — the Distance "—" empty-state dash is an accepted convention, not an a11y
  defect (agreed).
- **Bugs #8 and #10 parked** — moved to a new "Keyboard / Focus-management findings (PARKED)"
  section in bug-log.md (PARKED-KF-1 = Water/Weight focus leak; PARKED-KF-2 = Mood focus never
  enters). Per QA-lead direction, all keyboard-focus issues are parked and reproduced only on
  request; corresponding diary.md rows (INTAKE-TC-026, VIT-TC-027) marked ⏸️ Parked.
- **Bugs #11 (aria-current) and #12 (Freshchat Escape) ignored this cycle** per direction; diary.md
  rows (CHROME-TC-024/026) marked ⏸️ Set aside.
- Net active new-bug list after corrections: **#15 (P1), #13 (P2), #14 (P2), #7 (P3 enhancement)**;
  parked: KF-1/KF-2; withdrawn: #9; ignored: #11/#12. (Plus the P2-pass reproductions of already-
  known bugs, unchanged.)
