# Vantage Fit Web — Summary Page Bug Log

Environment: Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
Account: Demo/test tenant (CRUD safe)

---

## Section A: Header cluster — findings

## Bug #1 [Accessibility - P3]
[Header — mobile hamburger menu — "Vantage Points" toggle uses an unlabeled checkbox]
The "Vantage Points" row inside the mobile hamburger menu (which expands to reveal "Points
Statement"/"Redeem Points") is implemented as a bare `<input type="checkbox" id="vcWalletToggle">`
with no accessible label, `aria-label`, or `aria-expanded` state — confirmed via direct DOM
inspection (`document.querySelector('input[type=checkbox]')`).
Expected: An expand/collapse control should be exposed as a button with `aria-expanded`, or at
minimum have an accessible name describing its purpose (e.g. "Expand Vantage Points details").
Actual: Screen readers would announce this as an unlabeled, unchecked checkbox — confusing given
it's actually a disclosure toggle, not a real checkbox/form input.
Evidence: evidence/summary_02_mobile_hamburger_menu.png; DOM confirmed via `browser_evaluate`.

---
## Section C: Snapshot card — findings

## Bug #2 [Functional/Data - P2]
[Summary page — Snapshot card (Steps/Active Minutes) does not refresh in-place after logging an activity]
Logged a real activity (Hiking, 30 min, Active Minutes toggle ON) via +Add → Workout → Log Activity
while sitting on the Summary page. Immediately after the modal closed, the Snapshot card still showed
"Active Minutes 0/32 mins, 0%" — unchanged. Confirmed via Diary (same tab, same session) that the
activity genuinely saved correctly: Diary's own Snapshot showed "30/32 mins, 94%" and its Activities
card listed "Hiking · 3:38 PM · 30 mins". Returning to the Summary tab via in-app navigation (not a
hard reload) then correctly showed "30/32 mins, 94%" on Snapshot, and Trends' "Active Minutes" tile
recalculated its 7-day average from 30→34 mins/day.
Expected: Snapshot's own stats should update immediately after a successful Quick Add submission,
without requiring the user to navigate away and back.
Actual: Data is correct in the backend/Diary immediately; the Summary page's Snapshot card only picks
it up after a route re-entry (navigating to another tab and back), not in-place.
Note/Doubt: This is the same category of issue as a previously-logged Quick Add bug where the Summary
Trends widget needed a *hard reload* to refresh — here, only an in-app navigate-away-and-back was
needed (Snapshot updated via SPA nav, no full reload required). Worth checking whether these are the
same root cause (a shared Summary-data cache/observable that only re-fetches on route (re-)init) or
two distinct gaps.
Evidence: reproduced directly — see conversation trace (0/32/0% immediately after save → Diary
confirmed 30/32/94% saved correctly → Summary showed 30/32/94% after navigating to Diary and back).

## Bug #3 [Accessibility - P3]
[Summary page — Snapshot card — redundant duplicate "Open Diary" focus stop]
The entire Snapshot card is wrapped in `<article role="button" tabindex="0" aria-label="Open Diary">`,
and it also contains a real nested `<button>` (a chevron-right icon) whose accessible text is also
"Open Diary". Confirmed via DOM inspection and by focusing the article and pressing Tab: focus moves
from the article (announced "Open Diary") directly to the nested button (also announced "Open Diary")
— two consecutive, identically-labeled tab stops for what is functionally a single action.
Expected: Either the card wrapper or the inner chevron button should be the sole focusable element for
this action, not both.
Actual: Keyboard/screen-reader users encounter the same "Open Diary" action twice in a row.
Note/Doubt: Initially suspected this icon might be a broken/separate "info" affordance whose click was
being swallowed by the parent card (since Trends has a visually similar icon that correctly navigates
to a dedicated `/ng/fit/activity-stats` page) — verified via DOM inspection that this is NOT the case:
the icon is a plain chevron-right sharing the exact same "Open Diary" label/action as the card, so nothing
is actually broken here beyond the redundant tab stop. Contrast: Trends' equivalent icon has its own
distinct accessible name ("View Trends") and its card is not itself a clickable wrapper, so no
duplication there.
Evidence: DOM structure and Tab-key focus sequence confirmed via `browser_evaluate`, see conversation trace.

---
### Notes / Doubts (not bugs) — Section C
- **Steps/Active Minutes percentage math is correct**: 30/32 mins displayed as 94% (30/32 = 93.75%,
  correctly rounded).
- **"Open Diary" navigation works correctly** (`/ng/fit/summary` → `/ng/fit/summary/diary`).
- **Trends' info icon is functionally distinct and correct** — labeled "View Trends", navigates to a
  dedicated `/ng/fit/activity-stats` page; its card is not itself a clickable wrapper, so there is no
  redundant-focus-stop issue there (contrast with Bug #3).
- Motivational text ("You have been among the top 40% most active people...") copy reviewed — no
  grammar issues found.

---
## Section D: Trends widget — findings

## Bug #4 [Functional/Data - P2]
[Summary page — League banner shows a stale "7-day average active minutes" value that contradicts the Trends widget directly above it]
On the same page load (confirmed after a full hard reload, both desktop and mobile), the Trends
widget's "Active Minutes" tile shows **"34 mins/day"**, while the League banner immediately below it
reads **"Your 7-day average is 0 active minutes/day, placing you in the Bronze League."** — a direct,
same-page contradiction between two displays of what should be the same underlying metric (7-day
average active minutes), both viewable without scrolling on desktop.
Expected: Both the Trends tile and the League banner should read from the same, correctly-updated
7-day average.
Actual: Trends correctly recalculated to 34 mins/day after a new activity was logged; the League
banner's average is still frozen at 0, not reflecting the same data.
Note/Doubt: Not yet determined whether the League banner's "0" was always stale (never correctly
computed this session) or whether it specifically failed to pick up today's new activity the same way
Trends did. Also unconfirmed whether the League *tier* itself ("Bronze League") is being computed off
this same wrong 0-value average, which could misrepresent a user's actual league standing.
Evidence: reproduced directly on both desktop (1440×900, hard reload) and mobile (390×844) — see
conversation trace; both show Trends "34 mins/day" alongside the banner's "0 active minutes/day" in
the same accessibility snapshot.

---
### Notes / Doubts (not bugs) — Section D
- **Day-of-week bar chart heights are mathematically correct** across all 4 tiles — verified via DOM
  inspection of each bar's `height: X%` style. E.g. Active Minutes: today's 30 min renders as
  14.2857% height against a 210-min historical peak (30/210 = 14.2857%, exact match); Avg Sleep's
  bar for today correctly shows 0% (no sleep logged today, per Diary's "No Data" state) against a
  100% historical peak day.
- **No Water/Hydration or Weight trend tile exists** — confirmed still true (only Avg Steps, Active
  Minutes, Mindful Minutes, Avg Sleep). This matches a previously-logged gap from Quick Add testing
  (missing Water trend tile) — not re-filed as a new bug here, but noted as still-reproducible.
- **Trends' own info icon ("View Trends") is functionally correct and distinct** from Snapshot's
  redundant chevron (see Bug #3) — it navigates to a dedicated `/ng/fit/activity-stats` page with
  Steps/Active Minutes toggle and Week/Month/Year tabs.
- **`/ng/fit/activity-stats` "Week" tab uses a fixed Mon–Sun calendar week (e.g. "Mon 13" – "Sun 19")
  that includes future, not-yet-happened days**, unlike the Summary page's own Trends widget, which
  uses a trailing 7-day window ending today (e.g. "9 - 15 Jul"). Not filed as a bug — showing the full
  current calendar week (including future placeholder days) may be an intentional design choice for
  this dedicated stats page; flagging only as a definitional inconsistency between the two views, and
  this deeper stats page is a separate feature from the Summary page itself so was not tested further.
- **Mobile Trends layout renders cleanly** as a 2×2 grid, no overlap or truncation with other page
  content (contrast with the systemic floating-widget-overlap pattern seen elsewhere in Quick Add
  testing — that issue does not reproduce here since Trends sits within the normal page flow, not
  near the bottom-anchored FAB/chat widget).

---
### Notes / Doubts (not bugs) — Section A
- **Logo/"Home" nav link staying on `/ng/fit/summary` is correct, not a bug**: navigating directly to
  `/ng/fit` server-side redirects to `/ng/fit/summary` — verified via direct URL navigation before
  concluding this was expected behavior, not a broken link.
- **Email verification banner dismissal correctly persists** across a full page reload — confirmed
  not a session/cache artifact.
- **Redeem, Cart, Notification links** all navigate to their correct destinations
  (`/ng/redeem/giftcards`, `/ng/checkout/cart`, `/ng/myaccount/notification`).
- **Notification empty state** ("Sorry, you don't have any Notifications yet.") renders correctly.
- **Profile Image button menu** opens correctly with "Hi Tester99!", email, View Profile, Change
  City, My Vouchers, Site Tour, FAQ, Sign Out — not deep-tested beyond confirming the menu opens
  and lists expected items (Site Tour / My Vouchers / Change Password flows are account-wide
  features, arguably out of Summary-page scope).
- **Mobile header/hamburger parity is good**: mobile collapses Cart/Redeem/Vantage
  Points/Profile into a "≡" hamburger menu (top header shows only logo + notification bell); the
  slide-out panel correctly mirrors desktop's profile menu content (Vantage Points, Redeem Points,
  Change Password, My Vouchers, Notification, FAQ, Sign Out) — clean layout, no overlap issues.
