# Summary Page — Module / Element Testing Scope

**Environment:** Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
**Account:** Demo/test tenant — CRUD is safe.

**Priority lens (per user guidance):** for every stat/tile below, actively check for data-integrity
issues first — does it match Diary/other pages, does it update without a hard reload, is the
unit/value correct — before checking layout/a11y. Don't just log touch-target sizes as standalone
findings; only flag layout defects that are concretely visible (real overlap/spacing, not
measurements alone).

## A. Header cluster (shared chrome — already scoped in `dashboard/quick-add/SCOPE.md`)

| Element | Priority | Test status |
|---|---|---|
| Logo / "VFit US home" | P4 | ✅ Done — confirmed `/ng/fit` correctly redirects to `/ng/fit/summary` server-side (not a broken link) |
| Email verification banner + Close (×) + "here" link | P3 | ✅ Done — Close works, dismissal persists across reload; "here" link not clicked (would trigger a real resend action) |
| Top icons: Home, Redeem, Cart, Notification | P2 | ✅ Done — all navigate correctly; Notification empty state correct |
| Vantage Points widget | P3 | ✅ Done — navigates to /ng/redeem; mobile version found to use an unlabeled checkbox toggle (Bug #1) |
| Profile image button (menu) | P3 | ✅ Done — opens correctly with expected items |
| Nav tabs: Summary / Challenges / Programs / Community | P2 | ⬜ Pending (only Summary tab exercised so far) |
| Quick Add + Overflow menu | — | Out of scope here — see `dashboard/quick-add/SCOPE.md` |

## B. Page header

| Element | Priority | Test status |
|---|---|---|
| "Summary" heading | P4 | ✅ Done |
| Date subtitle (e.g. "Tuesday, 14 July 2026") — correctness vs. real date, timezone handling | P2 | ✅ Done — confirmed exact match against browser's real system clock |

## C. Snapshot card

| Element | Priority | Test status |
|---|---|---|
| "Open Diary" navigation (whole card or link) | P2 | ✅ Done — navigates correctly |
| "Snapshot" heading + info icon button (tooltip/modal content) | P3 | ✅ Done — icon is actually a duplicate "Open Diary" chevron, not a distinct info affordance; redundant focus stop found (Bug #3) |
| Steps stat: value/goal, %, progress ring — accuracy vs. Diary, refresh behavior after a new log | P1 | ✅ Done — percentage math correct; does NOT refresh in-place after logging (Bug #2) |
| Active Minutes stat: value/goal, %, progress ring — same checks | P1 | ✅ Done — same finding as Steps (Bug #2) |
| Motivational text ("You have been among the top X% most active people...") — accuracy, grammar | P3 | ✅ Done — no grammar issues found |
| Empty/zero state (no steps/active minutes logged) | P3 | ✅ Done — renders as plain 0/goal, 0% (no separate empty-state component needed) |

## D. Trends widget

| Element | Priority | Test status |
|---|---|---|
| "Trends" heading + info icon button | P3 | ✅ Done — icon labeled "View Trends", correctly navigates to dedicated `/ng/fit/activity-stats` page |
| Avg Steps tile: value, trend arrow (↑/↓), day-of-week mini chart, date range | P1 | ✅ Done — bar heights mathematically verified correct |
| Active Minutes tile: same | P1 | ✅ Done — updates correctly after nav-away-and-back; contradicts League banner (Bug #4) |
| Mindful Minutes tile: same — cross-check against Guided Meditation completions (known gap, Bug #30) | P1 | ✅ Done — bar math correct; not re-tested against a fresh meditation completion this pass (prior Bug #30 from Quick Add still stands) |
| Avg Sleep tile: same | P1 | ✅ Done — bar math correct (0% today matches Diary's "No Data") |
| Missing tiles check: no Water/Hydration tile exists (known gap, Bug #35) — confirm still true, look for others (e.g. Weight trend) | P2 | ✅ Done — confirmed still true; no Weight trend tile either |
| Trend arrow direction logic — does ↑/↓ actually correspond to real week-over-week change | P2 | ✅ Done — arrows consistent with directional change (0-value metrics show ↓, increased metrics show ↑); exact prior-period comparison values not independently re-derived |
| Day-of-week bar chart — does highlighted/filled bar match today and real logged values | P2 | ✅ Done — verified via DOM height % against known logged values, exact match |
| Stale-data check: log new data elsewhere, return to Summary without hard reload — does Trends update (known systemic gap, Bug #8) | P1 | ✅ Done — Trends itself updates via SPA nav-away-and-back; found League banner does NOT update in sync (Bug #4) |

## E. Challenges & Badge

| Element | Priority | Test status |
|---|---|---|
| "Challenges" heading | P4 | ⬜ Pending |
| Empty state: "No active challenges" + subtext | P3 | ⬜ Pending |
| Active challenge card(s) (when present) — data accuracy, progress, navigation | P2 | ⬜ Pending |
| "Your latest badge" / Challenge Completion Badge card — image render, title, date correctness | P3 | ⬜ Pending |
| Navigation from badge/challenge card to detail view | P2 | ⬜ Pending |

## F. Right column — League / Vitals / Health

| Element | Priority | Test status |
|---|---|---|
| League banner ("Your 7-day average is X active minutes/day, placing you in the Bronze League") — accuracy of computed average and league tier | P1 | ⬜ Pending |
| League banner navigation (does it link to Bronze League info — cross-ref Overflow menu's "Bronze League" item, still pending) | P2 | ⬜ Pending |
| Vitals card: Weight value + "Updated on <date>" — accuracy vs. Diary/Update Weight modal (known gap, Bug #40) | P1 | ⬜ Pending |
| Health card: Lifestyle Score value + label (e.g. "Poor") — accuracy, what drives the score, does it update after new logs | P1 | ⬜ Pending |
| Empty/zero states for Vitals/Health (no weight ever logged) | P3 | ⬜ Pending |

## G. Highlights (community feed)

| Element | Priority | Test status |
|---|---|---|
| "Highlights" heading + subtitle | P4 | ⬜ Pending |
| "View all highlights" / "See all highlights" button — correct navigation | P2 | ⬜ Pending |
| Highlight card: author, date, content/image render, Likes count, Comments count | P2 | ⬜ Pending |
| Clicking a highlight card — does it open the right detail/thread | P2 | ⬜ Pending |
| Empty state (no highlights) | P3 | ⬜ Pending |
| Data freshness — do Like/Comment counts update live or require reload | P2 | ⬜ Pending |

## H. Footer / global chrome (bottom of Summary page)

| Element | Priority | Test status |
|---|---|---|
| Mobile sign-in QR code + instructions — does QR actually work / correct deep link | P3 | ⬜ Pending |
| Tagline ("Sweat now, Shine later.") | P4 | ⬜ Pending |
| "Need Help with Vantage Fit?" button | P3 | ⬜ Pending |
| "Go back to the Top" button | P4 | ⬜ Pending |
| Footer links: FAQ, Contact us, Privacy policy, Terms & conditions, App Store, Google Play | P3 | ⬜ Pending |
| Copyright text | P4 | ⬜ Pending |
| "Chat with us" (Freshchat) widget — functional, and check for overlap with page content on mobile (known systemic pattern, Bugs #24/#28/#45) | P2 | ⬜ Pending |

## I. Cross-cutting checks (apply to every element above)

- Responsive: desktop (1440×900) and mobile (390×844)
- Data reflection: does each stat match Diary's equivalent value; does it update without a hard reload
- Accessibility: labels on icon-only buttons (info icons, chevrons), focus order, contrast
- Loading/error states on initial page load (slow network, failed API call)

---
Status legend: ⬜ Pending · 🔵 In progress · ✅ Done · ⛔ Blocked · ⏸️ Held back
