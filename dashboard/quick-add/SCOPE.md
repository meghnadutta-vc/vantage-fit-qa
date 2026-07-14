# +Add (Quick Add) Header — Module / Submodule Testing Scope

**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Account:** Demo/test tenant — CRUD is safe, fully exercised (real saves verified, not just cancel).

## Header top-right cluster

| # | Control | Type | In scope? |
|---|---|---|---|
| A | **+Add ("Quick add")** button | Primary — opens 4-tab dropdown | ✅ Primary focus |
| B | **Overflow icon button** (unlabeled) | Secondary menu | ✅ Included (same cluster) |

## A. +Add → Workout tab

| Submodule | Web behaviour (expected) | Priority | Test status |
|---|---|---|---|
| Sync Steps History | "Track on app" — mobile hand-off | P2 | ✅ Done — 2 bugs (#1, #2), 1 a11y bug (#3) |
| Measure Heart Rate | "Track on app" — mobile hand-off | P2 | ✅ Done — reproduces #1/#2 |
| Track Squats | "Track on app" — mobile hand-off | P2 | ⬜ Pending |
| Log Activity | Web modal (manual activity entry) | P1 | ✅ Done — Book Reading, Hiking, Swimming, Strength/Weight Training→Bench Press, Yoga, Post Coffee Walk tested; 3 bugs (#7 duplicate catalog, #8 stale Summary, #9 "View all" wrong flow) |
| Start Outdoor Workout | "Track on app" — mobile hand-off | P2 | ⬜ Pending |
| Start 7-Minute Workout | "Track on app" — mobile hand-off | P2 | ⬜ Pending |

## B. +Add → Mindfulness tab

| Submodule | Web behaviour (expected) | Priority | Test status |
|---|---|---|---|
| Track Mood | Web modal (mood picker) | P1 | ✅ Done — 4 bugs (#22 save-color inversion, #23 +Add pre-fill mismatch, #24 mobile overlap, #25 touch targets) |
| Log Sleep | Web modal (sleep duration) | P1 | ✅ Done — 4 bugs (#26 slider keyboard inaccessible, #27 +Add pre-fill mismatch, #28 mobile overlap, #29 touch targets); Summary/Diary data reflection confirmed |
| Guided Meditation | Web player / modal (verify) | P2 | ⬜ Pending |

## C. +Add → Log Diary tab

| Submodule | Web behaviour (expected) | Priority | Test status |
|---|---|---|---|
| Log Water | Web modal (water intake) | P1 | ⬜ Pending |
| Update Weight | Web modal (weight entry) | P1 | ⬜ Pending |
| Log Meal | "Track on app" — mobile hand-off | P2 | ⬜ Pending |

## D. +Add → Track Habits tab

| Submodule | Web behaviour (expected) | Priority | Test status |
|---|---|---|---|
| Log Smoking | Web modal (smoking log) | P1 | ⬜ Pending |

## E. Overflow menu

| Submodule | Expected | Priority | Test status |
|---|---|---|---|
| Bronze League | Opens league details/info | P2 | ⬜ Pending |
| Download App | App download link/QR/store redirect | P3 | ⬜ Pending |
| Vantage Fit Privacy Policy | Opens policy (page/new tab) | P3 | ⬜ Pending |
| Vantage Fit Terms of Usage | Opens terms (page/new tab) | P3 | ⬜ Pending |

---
Status legend: ⬜ Pending · 🔵 In progress · ✅ Done · ⛔ Blocked · ⏸️ Held back
