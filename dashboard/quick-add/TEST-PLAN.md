# Vantage Fit — Web Dashboard: "+Add" (Quick Add) Header — Test Plan

**Platform:** Web dashboard (Playwright MCP / browser)
**Area:** Fit content header — top-right action cluster, primary target **+Add (Quick Add)**
**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Login:** via `https://api.vantagecircle.com` → *Login* → demo account (credentials handled per CLAUDE.md; never logged)
**Account state:** `Tester99` / demo user, Bronze League, mostly empty data (0 steps, 0 active mins), unverified-email banner present.
**Date drafted:** 2026-07-14
**Author:** QA (Claude Code, acting Senior QA)

> ⚠️ **Production environment.** This is a live/prod tenant, NOT the UAT tenant used elsewhere in this repo.
> Quick Add actions **write real user data** (mood, sleep, water, weight, activity, smoking). Treat every
> "save" as a destructive/irreversible-ish action: log intent before writing, use realistic-but-obviously-test
> values, and do not spam duplicate entries. Prefer open → validate → cancel where a write isn't required.

---

## 1. Objective

Validate the **+Add (Quick Add)** menu in the Fit sub-navigation header — its structure, each
action's behaviour on **web**, UI/UX consistency against the design system, and accessibility — plus the
adjacent overflow menu that shares the same header cluster.

---

## 2. Scope

### Header top-right cluster (as observed)
The Fit sub-nav row (`Summary · Challenges · Programs · Community`) has a right-aligned action cluster:

| # | Control | Type | In scope? |
|---|---|---|---|
| A | **+Add ("Quick add")** button | Primary — opens 4-tab dropdown | ✅ Primary focus |
| B | **Overflow icon button** (unlabeled) | Secondary menu | ✅ Included (same cluster) |

> The **global** top banner (VFit logo, Home, Redeem, Cart, Notifications, Vantage Points, Profile menu)
> is a different header and is **out of scope** for this run unless you extend it.

### 2.1 In scope
- +Add button: open/close, toggle, active state, positioning, keyboard/focus.
- All 4 tabs and every action item within them (behaviour on web).
- Modals/flows that Quick Add actions launch (open, validate, save, cancel, re-open).
- "Track on app" items — verify web treatment (should guide to mobile, not silently fail).
- Overflow menu items: Bronze League, Download App, Privacy Policy, Terms of Usage.
- Cross-cutting: UI/UX, copy, accessibility, responsive, empty/error states.

### 2.2 Out of scope
- Actual data captured *on the mobile app* (Sync Steps, Heart Rate, Squats, Outdoor/7-min Workout, Log Meal) — web can only be verified up to the "use the app" hand-off.
- Global banner nav, footer, Redeem/rewards flows.
- Backend/data-accuracy validation of health metrics (flag as Note/Doubt if suspicious).
- Login/auth robustness (covered only enough to reach the homepage).

---

## 3. Module / Submodule breakdown (test scope map)

### A. +Add → **Workout** tab
| Action | Web behaviour (expected) | Priority |
|---|---|---|
| Sync Steps History | "Track on app" — mobile hand-off | P2 |
| Measure Heart Rate | "Track on app" — mobile hand-off | P2 |
| Track Squats | "Track on app" — mobile hand-off | P2 |
| **Log Activity** | Web modal (manual activity entry) | P1 |
| Start Outdoor Workout | "Track on app" — mobile hand-off | P2 |
| Start 7-Minute Workout | "Track on app" — mobile hand-off | P2 |

### B. +Add → **Mindfulness** tab
| Action | Web behaviour (expected) | Priority |
|---|---|---|
| **Track Mood** | Web modal (mood picker) | P1 |
| **Log Sleep** | Web modal (sleep duration) | P1 |
| **Guided Meditation** | Web player / modal (verify) | P2 |

### C. +Add → **Log Diary** tab
| Action | Web behaviour (expected) | Priority |
|---|---|---|
| **Log Water** | Web modal (water intake) | P1 |
| **Update Weight** | Web modal (weight entry) | P1 |
| Log Meal | "Track on app" — mobile hand-off | P2 |

### D. +Add → **Track Habits** tab
| Action | Web behaviour (expected) | Priority |
|---|---|---|
| **Log Smoking** | Web modal (smoking log) | P1 |

### E. Overflow menu (adjacent icon)
| Item | Expected | Priority |
|---|---|---|
| Bronze League | Opens league details/info | P2 |
| Download App | App download link/QR/store redirect | P3 |
| Vantage Fit Privacy Policy | Opens policy (page/new tab) | P3 |
| Vantage Fit Terms of Usage | Opens terms (page/new tab) | P3 |

---

## 4. Test approach

For **every** action, run this loop (per CLAUDE.md driving rules):
1. Read accessibility tree → open the +Add menu → select tab → click action.
2. **Verify state after action** — modal opened? correct title/fields? Re-read the tree, don't assume.
3. Screenshot each distinct state into `evidence/` with a descriptive name.
4. Exercise the modal: default state, valid input, **boundary/negative input**, save, cancel, re-open.
5. Confirm the write reflected on Summary (e.g. Water/Weight/Mood tiles) where visible; else Note/Doubt.
6. If blocked (mobile-only, paywall, dead-end) → log BLOCKED in coverage log, move on.

### Test types applied
- **Functional** — does each action do what it claims; save/cancel/validation.
- **UI/UX** — layout, spacing, alignment, typography, active/hover/disabled states, menu open/close, z-index/overlap, design-system consistency across tabs & modals.
- **Copy** — labels, "Track on app" wording, modal titles, button text, grammar/tone.
- **Accessibility** — icon buttons need labels (see §6), touch targets ≥44px, focus order, keyboard open/close/Esc, contrast, focus trap in modals.
- **Negative / boundary** — empty submit, 0 / negative / huge numbers, decimals, future dates, very long text.
- **Responsive** — menu & modals at desktop, tablet, narrow widths.
- **State** — empty, loading, success toast, error.

---

## 5. Detailed scenario checklist (per submodule)

### +Add button (container)
- [ ] Opens on click; closes on second click / outside click / Esc.
- [ ] Active/expanded state styled correctly; only one menu open at a time (vs overflow menu).
- [ ] Default tab = Workout; tab switching updates the list; active tab styled.
- [ ] Menu positioned within viewport; no clipping/overlap at narrow widths.
- [ ] Keyboard: focusable, Enter/Space opens, arrow/tab navigates items, Esc closes, focus returns to trigger.

### Web-actionable actions (Log Activity, Track Mood, Log Sleep, Guided Meditation, Log Water, Update Weight, Log Smoking)
- [ ] Correct modal/screen opens with correct title & fields.
- [ ] Valid input saves; success feedback shown; Summary/tile updates.
- [ ] Negative/boundary input handled gracefully (validation, no crash).
- [ ] Cancel/close discards without saving; re-open shows clean state.
- [ ] Units correct for this tenant (lbs vs kg — account shows **132.28 lbs**).
- [ ] Modal a11y: focus trap, labelled fields, Esc closes, contrast.

### "Track on app" actions (Sync Steps, Heart Rate, Squats, Outdoor Workout, 7-Min Workout, Log Meal)
- [ ] Web click gives clear guidance (QR/app prompt/redirect) — NOT a silent no-op or error.
- [ ] "Track on app" affordance is discoverable before clicking (label already visible — good).

### Overflow menu
- [ ] Each item opens the correct destination; external links open appropriately (new tab, correct URL).
- [ ] Bronze League detail matches the "Bronze League" state shown on Summary.

---

## 6. Observations already recorded during scoping (pre-plan)

These were seen while mapping and will be verified/expanded during execution:
- **A11y — unlabeled icon buttons:** the overflow icon button and the +Add trigger expose no accessible
  name in the tree (`button` with empty name). Likely missing `aria-label` / content-description. Candidate
  **P3 Accessibility** bug — to confirm.
- **Copy/verification banner:** persistent "verify your email … sent to Tester99" banner (account-state, not a bug).
- Data is empty/zero across the account — good for testing writes, but "top 40% most active" copy shows with
  0 activity — possible **Note/Doubt** on messaging logic.

Full bugs go to `bug-logs/bug-log.md`; coverage to `coverage-log.md`.

---

## 7. Deliverables & file layout

```
dashboard/quick-add/
  TEST-PLAN.md                         ← this file
  test-cases/
    workout.md                         ← Workout-tab actions (WRK-TC-###)
    mindfulness.md                     ← Mindfulness-tab actions (MND-TC-###)
    log-diary.md                       ← Log Diary-tab actions (DRY-TC-###)
    track-habits.md                    ← Track Habits-tab actions (HAB-TC-###)
    quick-add-menu.md                  ← menu container + overflow (QA-TC-###)
  bug-logs/bug-log.md
  coverage-log.md
  evidence/                            ← screenshots (quickadd_##_*.png)
```

Test-case files use the exact CLAUDE.md column format; **Status left blank** for human QA.

---

## 8. Proposed execution order

1. Menu container + tab-switching + overflow menu (structure & a11y).
2. Log Diary (Log Water, Update Weight) — simplest web writes, verifiable on Summary.
3. Mindfulness (Track Mood, Log Sleep, Guided Meditation).
4. Workout — Log Activity (web) + verify all "Track on app" hand-offs.
5. Track Habits (Log Smoking).
6. Compile bug log + coverage log + end-of-run report.

---

## 9. Risks / assumptions / open questions

- **Production writes** — need confirmation this demo account is safe to write test data to (assumed yes,
  but flagged; will use minimal, obvious test values).
- "Track on app" items cannot be completed on web — expected BLOCKED for the data outcome; web hand-off IS testable.
- reCAPTCHA blocks the standard login page; the `api.vantagecircle.com` login path was used instead — note for repeatability.
- Guided Meditation web behaviour unknown until exercised (player vs app hand-off).
