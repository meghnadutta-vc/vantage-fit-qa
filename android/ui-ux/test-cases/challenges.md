# Test Cases — Challenges

**Entry:** Bottom-nav **Challenges** tab (within NavigationActivity).
**Structure:** Three sub-tabs — **Ongoing · Upcoming · Past**. Tapping a challenge → **ChallengeInfoActivity** (detail: tasks, weekly progress, leaderboard). ⓘ on the detail → **AboutChallengeActivity** ("More info": T&C + About).
**Model:** Challenges appear to be **HR/admin-assigned** (empty state: "If your HR has enrolled you in a challenge recently…") — no self-join CTA found.
**Account state:** Demo account has **0 Ongoing, 0 Upcoming, 5 Past** challenges (all Past show 0% progress / score 0).
**Build:** VFit PROD new design fixes 16_jun.apk · emulator-5554, Android 16 (API 36).

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| CHAL-TC-001 | Open Challenges tab | On dashboard | Tap "Challenges" in bottom nav | Challenges section opens with Ongoing/Upcoming/Past tabs | Opens; 3 tabs shown ✓ | | P2 |
| CHAL-TC-002 | Loading state | Opening Challenges | Observe initial render | Skeleton/shimmer placeholders while loading, then content | Skeleton loaders shown (~4s) then content ✓ | | P3 |
| CHAL-TC-003 | Ongoing — empty state | No ongoing challenges | View Ongoing tab | Clear empty state + clear next action | "No Ongoing Challenges" + copy + Refresh; but copy says "Choose an option below" with only one option (Bug #48) | | P4 |
| CHAL-TC-004 | Ongoing — Refresh | Ongoing empty | Tap "Refresh" | Re-fetches; shows challenges or stays empty | Re-fetches; stays empty (account has none) ✓ | | P3 |
| CHAL-TC-005 | Upcoming — empty state | No upcoming challenges | Tap "Upcoming" | Clear empty state + Refresh | "No Upcoming Challenges Found" + copy + Refresh ✓ (title phrasing differs from Ongoing — Bug #51) | | P4 |
| CHAL-TC-006 | Past — list renders | Has past challenges | Tap "Past" | List of past challenges with title, rank, dates, thumbnail | 5 challenges render (Stress Free Month, Adherence Task III/II/Tasks, June Fitness) ✓ | | P3 |
| CHAL-TC-007 | Tab switching | On Challenges | Switch Ongoing↔Upcoming↔Past repeatedly | Correct content per tab; selected tab underlined; no crash | Switches correctly; red underline on active tab ✓ | | P3 |
| CHAL-TC-008 | Open a past challenge detail | Past list shown | Tap "Stress Free Month" | ChallengeInfoActivity opens with hero, progress, tasks, leaderboard | Opens; all sections present ✓ | | P2 |
| CHAL-TC-009 | Detail — header & status | On detail | Read hero + status | Title, theme/week, weekly rank, description, "Ended" status | Shows title, "Theme of the week / Week 1", Weekly Rank 10143, description, "This challenge has Ended" ✓ | | P3 |
| CHAL-TC-010 | Detail — weekly progress | On detail | Read progress bar | Progress reflects completion (0% here) | "Weekly progress: 0%" + bar at 0 ✓ | | P3 |
| CHAL-TC-011 | Detail — tasks list | On detail | Scroll to Week 1 Tasks | Each task: icon, description, n/total, Completed | 4 tasks shown with circular progress (0/3, 0/3, 0/7, 0/4) ✓ | | P3 |
| CHAL-TC-012 | Task description pluralization | Challenge with a "1 day" task | Read task copy | Correct singular/plural ("1 day", "3 days") | "Read 10 pages **1 days** this week" — wrong plural (Bug #49) | | P4 |
| CHAL-TC-013 | Task row interactivity | On ended challenge detail | Tap a task row | Either expands a daily breakdown or is inert (ended) | Task rows not clickable (display-only on ended challenge) ✓ | | P4 |
| CHAL-TC-014 | Detail — leaderboard | On detail | Scroll to Leaderboard | YOU highlighted with rank+score; ranked list of participants | Shows "YOU" (10143rd, score 0) + ranks 1–5 (User10136…), all score 0 ✓ | | P3 |
| CHAL-TC-015 | Leaderboard ranking sanity (edge) | All scores tied at 0 | Compare YOU's rank vs others' scores | Tied scores rank fairly / sensibly | YOU = 10143rd at score 0 while #1 also score 0; rank falls back to user-ID order (Bug #50) | | P3 |
| CHAL-TC-016 | Challenge "More info" (ⓘ) | On detail | Tap the ⓘ icon (top-left of hero) | Opens T&C + About | AboutChallengeActivity: "Standard Terms and Conditions of VantageFit Apply." + About "Complete the assigned tasks every week" ✓ | | P3 |
| CHAL-TC-017 | Detail layout consistency | Multiple past challenges | Open a 2nd challenge (Adherence Task III) | Same detail layout/structure | Consistent layout ✓ (note: some challenges show no description line) | | P3 |
| CHAL-TC-018 | Back navigation | In detail / more-info | Press Back | Returns to previous screen with state intact | Back works: More info→detail→Past list→dashboard ✓ | | P3 |
| CHAL-TC-019 | Accessibility — tab & control labels | TalkBack | Focus tabs, ⓘ, Refresh, task rows | Each announces a meaningful label | Spot-check only; full TalkBack pass not run (see coverage) | | P3 |
| CHAL-TC-020 | Join / enroll in a challenge | (no active challenges) | Look for a join/browse CTA | Ability to discover & join a challenge | No self-join/browse CTA found (HR-assigned model) — NOT testable on this account | | P3 |
| CHAL-TC-021 | Active (Ongoing) challenge — task completion & live progress | An enrolled, in-progress challenge | Complete a task; watch progress/score/rank update | Progress, score, and rank update; leaderboard reflects it | **NOT TESTABLE** — account has 0 ongoing challenges (data gap) | | P2 |
| CHAL-TC-022 | Upcoming challenge detail | An upcoming challenge exists | Open an upcoming challenge | Shows start date / pre-start state | **NOT TESTABLE** — 0 upcoming challenges (data gap) | | P3 |
