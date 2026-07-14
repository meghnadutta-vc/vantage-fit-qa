# Vantage Fit Web — +Add (Quick Add) Bug Log

Environment: Production — `https://fitvantage.vantagecircle.com/ng/fit/summary`
Account: Demo/test tenant (CRUD safe)

---

## Bug #1 [Functional/UX - P3]
[+Add → Workout tab — Sync Steps History / Measure Heart Rate — "Continue in app" modal]
Clicking a "Track on app" action (Sync Steps History, Measure Heart Rate — likely all "Track on app"
items) opens the "Continue this in the Vantage Fit app" QR modal, but the Quick Add dropdown menu
underneath it does **not** close. The dropdown (tabs + all 6 Workout action rows) stays fully rendered
and visible behind/around the modal, overlapping the Trends widget on the page.

Expected: Opening the modal should close the Quick Add dropdown (consistent with normal outside-click
behavior, which does close it correctly).
Actual: Dropdown remains open and visible behind the modal. Confirmed reproducible on both
Sync Steps History and Measure Heart Rate.
Note/Doubt: Pressing **Esc** correctly closes both the modal and the dropdown together — so the
close-all behavior exists but is only wired to the Esc key, not to the modal's own open/close lifecycle
or its Close (X) button.
Evidence: evidence/workout_syncsteps_01_modal.png, evidence/workout_syncsteps_02_modal_viewport.png,
evidence/workout_heartrate_01_modal.png

## Bug #2 [Functional/UX - P3]
[+Add → Workout tab — "Continue in app" modal — Close (X) button]
The modal's **Close (X)** button only closes the modal itself; it does not close the Quick Add dropdown
that was left open behind it (see Bug #1). This is inconsistent with the **Esc** key, which closes both
together.
Expected: Close (X) button and Esc key should produce the same end state.
Actual: Close (X) → dropdown remains open. Esc → dropdown also closes.
Evidence: evidence/workout_syncsteps_02_modal_viewport.png

## Bug #3 [Accessibility - P2]
[+Add → Workout tab — "Continue in app" modal — focus management]
When the "Continue this in the Vantage Fit app" modal is opened via **keyboard** (Enter on
"Sync Steps History"), keyboard focus is not moved into the modal dialog. `document.activeElement`
remains the underlying "Sync Steps History" trigger button rather than shifting to the modal's Close
button or another focusable element inside it.
Expected: Per standard modal/dialog accessibility pattern (WCAG 2.4.3, WAI-ARIA dialog practices),
opening a modal should move focus inside it (typically to the close control or first focusable element)
and trap focus there until closed.
Actual: Focus stays on the page behind the modal. A keyboard-only or screen-reader user has no
programmatic indication the modal opened, and continuing to Tab would move through the underlying
page, not the dialog.
Evidence: verified via `document.activeElement` check after Enter-activating Sync Steps History
(see kbd_focus_quickadd.png for the pre-activation focus state).

## Bug #4 [Accessibility - P3]
[+Add header cluster — icon buttons unlabeled / mislabeled]
- **Desktop:** The overflow "..." icon button next to +Add and the +Add trigger itself expose icon-only
  controls; the overflow button has no visible/accessible text label distinguishing its purpose beyond
  an icon.
- **Mobile (≤390px viewport):** The equivalent "+Add" floating action button is exposed to assistive
  tech with the accessible name **"Give recognition"**, which is incorrect — activating it opens the
  Quick Add bottom sheet (Workout/Mindfulness/Log Diary/Track Habits), not a recognition/kudos flow.
Expected: Accessible name should describe the actual action ("Quick add" / "Add", matching desktop).
Actual: Mislabeled as "Give recognition" on mobile — screen reader users are told the wrong function.
Evidence: evidence/quickadd_06_mobile_bottomsheet_mislabel.png

## Bug #5 [Accessibility - P3]
[+Add header cluster — touch target size]
Measured via bounding boxes at 1440×900 desktop viewport:
- **+Add ("Quick add") trigger button:** 59×29px
- **Overflow "..." icon button:** 29×29px
- **Workout/Mindfulness/Log Diary/Track Habits tab buttons (inside dropdown):** ~29px height each
All fall below the widely-used 44×44px (WCAG 2.5.5 / Android 48dp / iOS 44pt) minimum touch-target
guideline. The list action rows themselves (e.g. "Sync Steps History") measure 458×50px and are fine.
Expected: Interactive controls ≥44×44px, especially in a primary action cluster.
Actual: Trigger button, overflow icon, and in-menu tab buttons are all ~29px tall.
Note/Doubt: Severity may be judged lower on desktop-mouse-primary flows; flagging as P3 since this is
a design-system consistency check per the rollout's accessibility focus.
**Update (re-tested):** the "Continue this in the Vantage Fit app" modal's own **Close (X) button**
also measures **30×30px**, identically on both Sync Steps History and Measure Heart Rate — same
pattern, confirming this touch-target issue extends beyond the +Add trigger cluster into modal
chrome as well.

## Bug #6 [UI/State - P4]
[+Add → "Continue in app" modal — behavior on viewport resize]
Resizing the browser viewport (1440×900 → 390×844) while the "Continue this in the Vantage Fit app"
modal is open causes the modal to disappear silently with no transition/close animation, with no
programmatic indication to the user that it was dismissed. The page also fully re-renders into the
mobile layout at the same time, so this may be an artifact of the responsive re-render rather than a
distinct modal bug.
Expected: Ideally modal should either persist responsively or close explicitly with feedback.
Actual: Modal vanishes silently on resize.
Note/Doubt: Low severity since this modal held no user-entered data to lose; would be more serious if
reproduced on a form-based Quick Add modal (e.g. Log Activity, Track Mood) — to verify in later modules.
Evidence: evidence/workout_syncsteps_03_modal_mobile.png

## Bug #7 [Functional/Data - P2]
[+Add → Workout tab → Log Activity — "Post Coffee Walk" duplicated 6×]
Inside the Log Activity modal, under "Cardiovascular Activities", the entry **"Post Coffee Walk"**
appears **6 times in a row**, identical in icon, label, and behavior (each opens the same activity
logging form). Reproduced on two separate fresh opens of the modal (not a one-off render glitch).
Expected: Each cardiovascular activity should appear once in the list.
Actual: "Post Coffee Walk" is duplicated 6×, cluttering the list and likely indicating a data/config
issue in the activity catalog (e.g., a seed/config array with repeated entries, or a broken loop key).
Evidence: evidence/logactivity_00_initial_state.png, evidence/logactivity_01_modal_top.png

## Bug #8 [Functional/UX - P2]
[+Add → Workout tab → Log Activity — Summary page stale after logging]
After successfully logging an activity via Log Activity (verified: Hiking, 30 min, Active Minutes
toggle ON), the Summary page's Snapshot tile ("Active Minutes 0/32 mins, 0%") and Trends widget do
**not** refresh to reflect the new data — even after in-app SPA navigation away (to Diary, where the
data displays correctly: 30/32 mins, 94%) and back to Summary via the tab. Only a full page reload
(`browser navigate` to the same URL) shows the correct updated values (30/32 mins, 94%; Trends "Active
Minutes 4 mins/day ↑").
Expected: Summary tile/widgets should refresh immediately after a successful Quick Add submission,
or at minimum on any navigation back to the Summary tab.
Actual: Data is correct in the backend (confirmed via Diary) but the Summary view displays stale
cached values until a hard reload.
Evidence: evidence/logactivity_07_hiking_submitted.png (stale) vs. Diary snapshot (correct) vs.
post-reload snapshot (correct) — see conversation trace.

## Bug #9 [Functional - P2]
[Diary → Activities section — "View all" opens wrong flow]
On the Diary page, the "Activities" card shows "N logged" with a **"View all ↗"** link. Clicking
"View all" is expected to show the full list of activities already logged for the day (the visible
list is truncated to 3 items). Instead, it opens the **Log Activity** modal — the same "add a new
activity" picker reachable from +Add — with no relation to viewing the existing log.
Expected: "View all" should expand/navigate to the complete list of the day's logged activities.
Actual: Opens the unrelated "add new activity" picker; the user has no way (found so far) to see
activities beyond the first 3 in the truncated list.
Evidence: reproduced directly — see conversation trace (Diary page, 6 activities logged, only Hiking/
Swimming/Yoga visible before truncation).

## Bug #10 [Accessibility - P3]
[+Add → Workout tab → Log Activity — stepper/control touch targets too small]
Measured via bounding boxes across every Log Activity sub-form tested (Book Reading, Hiking, Swimming,
Bench Press, Yoga, Post Coffee Walk), reproduced on both desktop (1440×900) and mobile (390×844)
viewports:
- **Duration −/+ steppers:** 30×30px (every activity)
- **Calories −/+ steppers:** 30×30px (every activity)
- **Distance −/+ steppers (Hiking):** 30×30px
- **km/mi unit radio buttons (Hiking):** ~34×22px and ~29×22px
- **Modal Close (X) button:** 32×32px (category list and every sub-form)
All fall below the 44×44px (WCAG 2.5.5 / Android 48dp / iOS 44pt) minimum touch-target guideline —
consistent with Bug #5 found earlier on the +Add trigger/overflow icon, but here affecting every
interactive stepper control across the entire Log Activity feature, on both desktop and mobile.
Expected: Interactive controls ≥44×44px, especially numeric steppers meant for repeated tapping.
Actual: All steppers/toggles/close buttons measured are 30–32px, unchanged between desktop and mobile
viewports (no responsive touch-target scaling).
Note/Doubt: The +/- glyph color (light pink/red on a pale gray circle) also looked visually low-contrast
in review screenshots — flagging as a Note/Doubt since exact contrast ratio wasn't measured, not a
confirmed bug.
Evidence: evidence/ui_bookreading_desktop.png, evidence/ui_bookreading_mobile.png (bounding-box data
captured via accessibility snapshot, see conversation trace)

## Bug #11 [Functional/UX - P2]
[+Add → Workout tab → Log Activity — sub-form state lost on viewport resize]
While inside an activity's detail sub-form (e.g., "Book Reading" with Date/Time/Duration/Calories
fields filled in), resizing the browser viewport (1440×900 → 390×844, simulating a device rotation or
window resize) causes the Log Activity modal to **reset back to the root category list** (Well Being /
Most Popular / Cardiovascular Activities / etc.), discarding the in-progress sub-form and any
adjustments made to Duration, Calories, Date, or Time.
Expected: The modal should preserve its current navigation state (which activity's form is open, and
any field adjustments) across a viewport/orientation change, or at minimum warn before discarding.
Actual: Silent reset to the category list with no confirmation — any user-adjusted values are lost.
Note/Doubt: This is a related but distinct failure mode from Bug #6 (generic "Continue in app" modal
disappearing entirely on resize) — here the modal survives but loses its internal navigation/form
state instead, which is worse since this modal does hold user-entered data.
Evidence: reproduced directly — see conversation trace (Book Reading form open → resize → root
category list, confirmed via accessibility snapshot).

## Bug #12 [UI/Accessibility - P3]
[+Add → "Continue in app" modal — "Save QR" primary button undersized & inconsistent with rest of app]
Measured via bounding boxes on both Sync Steps History and Measure Heart Rate (identical modal):
the **"Save QR"** button — the modal's sole primary call-to-action — measures **84×27px**. This is
well under the 44×44px touch-target minimum, and is visually inconsistent with every other primary
CTA button tested in the Log Activity flows (e.g. "Log activity" buttons measure 375×50px across all
6 activity sub-forms tested).
Expected: Primary CTA buttons should be sized consistently across the app (~50px height) and meet the
44×44px touch-target minimum.
Actual: "Save QR" is roughly half the height of comparable primary buttons elsewhere in the same
Quick Add feature.
Evidence: evidence/ui_syncsteps_desktop.png, evidence/ui_syncsteps_mobile.png (bounding-box data
captured via accessibility snapshot for both Sync Steps History and Measure Heart Rate — identical
84×27px in both cases, confirming it's not activity-specific).

## Bug #13 [Functional/UX - P2]
[+Add → Workout tab → Log Activity — Time picker silently clamps to current time, then leaves Duration/Calories stale]
In any Log Activity sub-form (reproduced on Book Reading), selecting an hour/minute/AM-PM combination
that would place the activity in the future (relative to the real current time, since Date=Today)
is **silently rejected and clamped** to the current time — e.g. attempting to set 3:15 PM while the
real time was ~12:44 PM resulted in the field silently resetting to "12:44 PM" with no error message,
toast, or inline indication that the selection was invalid.
Worse, this clamp has an **unexplained side effect**: Duration dropped from 45 min to 5 min (and
Calories recalculated to match), and **did not restore** even after subsequently selecting a clearly
valid, much-earlier time (9:00 AM) — Duration stayed frozen at 5 min and had to be manually incremented
back up via the "+" stepper.
Expected: Either (a) prevent selecting invalid hour/minute values outright (e.g. disable/hide future
slots, which the picker already does correctly for whole hours — see Note below), or (b) show a clear
message when a selection is auto-corrected. Duration should not be silently altered by a Time change,
or if intentionally coupled, should recalculate correctly when a new valid Time is chosen.
Actual: Silent clamp with no feedback; Duration left stale at the clamped value indefinitely.
Note/Doubt: The underlying "don't allow future timestamps" business rule itself is sound and consistent
with the Date picker's behavior (which correctly hides future dates) — the bug is the silent
clamping/feedback gap and the stuck Duration, not the validation rule itself.
Evidence: reproduced directly — see conversation trace (hour "3" + minute "15" + PM → silently became
"12:44 PM", Duration 45→5 min; switching to AM showed all hours/minutes enabled since any AM time
today is in the past).

## Bug #14 [Accessibility - P3]
[+Add → Workout tab → Log Activity — stepper glyph contrast fails WCAG AA]
Measured contrast ratio of the pink/red "−"/"+" glyphs (rgb(241,81,98)) against their circular
background (~rgb(238,242,247)) using the standard WCAG relative-luminance formula: **3.07:1**.
This fails the WCAG AA minimum of **4.5:1** for normal text (and is below even the relaxed 3:1
threshold typically reserved for large text/graphical UI components, given these are small glyphs).
Expected: Text/icon contrast ≥4.5:1 against its background.
Actual: 3.07:1 — confirmed via canvas-based RGB extraction + relative luminance/contrast calculation,
not just visual impression.
Evidence: computed via `document` canvas color-parsing in an evaluate script; see conversation trace.

## Bug #15 [Copy - P4]
[Global — grammar issues in copy encountered during this testing pass]
Two grammar/punctuation issues found while reviewing all copy surfaced during Quick Add / Log Activity
testing:
1. **Email-verification banner** (persistent top banner, all pages): "Thank you for your registration,
   Please verify your email address by clicking on the verification link sent to Tester99. If you
   have not received any email, please click here." — comma splice: "registration, Please verify" joins
   two independent clauses with a comma and an incorrectly capitalized "Please". Should be a period:
   "...your registration. Please verify your email address..."
2. **Summary page Snapshot card**: "You have been among the top 40% most active people in Vantage Fit
   past 7 days." — missing preposition; reads awkwardly. Should be "...in Vantage Fit **over the**
   past 7 days" or "...in Vantage Fit **in the** past 7 days."
Expected: Grammatically correct, properly punctuated copy throughout.
Actual: Comma splice in the verification banner; dropped preposition in the Snapshot card subtext.
Note/Doubt: Copy/tone judgment call — these are clear grammar errors, not stylistic preferences.
Evidence: both strings captured verbatim in accessibility snapshots throughout this session (visible
on every page — Summary, Diary, Log Activity modal background, etc.).

---
## Bugs reported directly by QA (Meghna) — added verbatim below, with AI verification notes where checked

## Bug #16 [UX/Consistency - P3] — QA-reported
[Log Activity (and possibly elsewhere) — Date picker orientation inconsistent with rest of app]
The Date picker inside Log Activity sub-forms (e.g. Book Reading, Hiking) displays **today first,
then past dates going to the right** as you read/scroll the horizontal date strip (confirmed order:
"TU 14 Jul" → "MO 13 Jul" → "SU 12 Jul" → "SA 11 Jul" → ... → "MO 15 Jun"). This is the reverse of the
timeline convention used elsewhere in the app/web/dashboard, where past dates are placed to the
**left** of the current date.
Expected: Consistent timeline orientation across the app — past dates to the left, current date
anchored appropriately, matching the convention used elsewhere.
Actual: Log Activity's date strip puts today first and past dates increasingly to the right.
QA verification: **Confirmed independently** — this matches the date-button order captured directly
from the accessibility tree during earlier Log Activity testing (see Book Reading date-picker capture).

## Bug #17 [UX/Consistency - Enhancement] — QA-reported
[Log Activity date picker — future dates should be disabled, not simply absent]
For consistency with the rest of the app/web/dashboard's date pickers (which show future dates but
render them **disabled/greyed out**), the Log Activity date picker should follow the same pattern
instead of just omitting future dates entirely from the strip.
Expected: Future dates visible but disabled, consistent with other date pickers across the product.
Actual: Future dates are not rendered at all in this picker (behaviorally prevents future selection,
but via a different UI pattern than the rest of the app).
QA verification: Not independently re-confirmed against other date pickers elsewhere in the app this
session — logged as reported. Recommend a follow-up pass comparing this picker against Track Mood,
Log Sleep, Log Water, Update Weight, and Log Smoking's date pickers for consistency.

## Bug #18 [Functional - P1] — QA-reported
[Log Activity — future-dated activity logging is possible]
QA reports that it is possible to log an activity against a **future date**, which should not be
allowed.
Expected: Future-dated activity entries should be blocked, consistent with the past-only date
strip and the Time picker's current-time clamp (Bug #13).
Actual: Reported as possible by QA.
QA verification: **Not independently reproduced this session** — my own testing of the Book Reading/
Hiking date picker only ever offered past dates (today back to ~1 month), with no future date visibly
selectable, and I did not find another entry point to set a future date. Flagging as reported-but-
unverified; needs a specific repro path (which activity/module, which field) to confirm and file
correctly. Do not treat as confirmed until reproduced.

## Bug #19 [UX - P3] — QA-reported
[Log Activity — Time picker scroll bleeds into background page scroll]
QA reports that scrolling the hour/minute wheel inside the Time picker (while the Log Activity modal
is open) also causes the background Summary page to scroll slightly — i.e. the scroll action isn't
fully contained within the modal/picker.
Expected: Scrolling within an open modal's internal control (the time wheel) should be fully contained;
the background page should not move at all.
Actual: Reported as a slight background scroll accompanying the modal's internal scroll.
QA verification: Attempted to reproduce via a synthetic `WheelEvent` dispatch in this session — the
background did not scroll, but this method does not reliably reproduce real trackpad/mouse scroll-
chaining behavior (synthetic events don't trigger the browser's native overscroll/scroll-chaining the
way physical input does). **Inconclusive** — logging as reported, needs manual verification with real
scroll input (mouse wheel or trackpad) rather than automated tooling.

## Bug #20 [Enhancement] — QA-reported
[Log Activity — post-save redirect lands on Summary instead of showing the logged activity]
After saving an activity via "Log activity", the modal closes and the user lands on the Summary page.
QA suggests this should instead keep the user within the Log Activity context, or show the
just-saved activity in a list/confirmation view, rather than dropping them back to Summary.
Expected (suggested): After save, either stay on a Log Activity-related view or show the saved
activity in a listing/confirmation state.
Actual: Modal closes; user lands on Summary (confirmed behavior from this session's testing).
Note: Filed as **Enhancement** per QA's own categorization — a UX improvement suggestion, not a defect.

## Bug #21 [Navigation/UX - P2] — QA-reported (priority: address first)
[+Add → Workout tab — no way back to previous state when closing a directly-opened activity/hand-off modal]
QA reports: when clicking any Workout-tab item other than "Log Activity" (e.g. Sync Steps History,
Measure Heart Rate, Track Squats, Start Outdoor Workout, Start 7-Minute Workout), the +Add dropdown
disappears once its modal opens; then closing that modal (X) exits **all the way back to Summary**,
with no way to return to the previous state (the Quick Add dropdown / Workout list). QA requests a
"Back" control for this flow, similar to the one already present inside Log Activity's own sub-forms.
Expected: Closing a directly-opened activity/hand-off modal should return to the previous state (the
Quick Add dropdown), not exit all the way to Summary.
Actual: Reported as exiting fully to Summary with no intermediate back state.
QA verification note: This session's own earlier testing (Bugs #1/#2) found that for the generic
"Continue this in the Vantage Fit app" hand-off modal (Sync Steps History / Measure Heart Rate),
closing via (X) actually leaves the Quick Add dropdown **open behind the modal** rather than exiting
to Summary — the opposite of what's described here. Separately, the "Back" button *inside* Log
Activity's own sub-forms (e.g. Hiking → Back → returns correctly to the category list) was verified
working correctly this session. There may be a genuine gap specifically for **Track Squats** or the
mobile bottom-sheet flow that wasn't covered by prior testing — recommend re-testing this exact
scenario (which specific item, desktop vs. mobile) to pin down the precise repro before filing further,
since current findings partially conflict with this report.

---
## Mindfulness → Track Mood — findings (AI-tested this session)

## Bug #22 [UI/UX - P2]
[Mindfulness → Track Mood — Save button color inverted relative to mood sentiment]
Confirmed by selecting each of the 5 moods and capturing a screenshot of the resulting Save button
color:
- Horrible (worst) → **green**
- Not Good → orange/amber
- Okay → peach/orange
- Pretty Nice → coral/salmon
- Awesome (best) → **red/coral-red**
This is a clear gradient tied to mood position, but it runs in the **opposite direction** of standard
UX color convention, where green universally signals positive/good and red signals negative/bad/danger.
Here green is bound to the worst mood and red to the best mood.
Expected: Color should reinforce sentiment — e.g. warm/red tones for negative moods, green tones for
positive moods (or a neutral, non-alarming palette that doesn't invoke danger/success semantics at all).
Actual: Inverted — confirmed via direct screenshot comparison across all 5 mood states.
Evidence: evidence/trackmood_02_prettynice_selected.png, evidence/trackmood_03_horrible_selected.png,
evidence/trackmood_04_notgood.png, evidence/trackmood_05_okay.png, evidence/trackmood_06_awesome.png

## Bug #23 [Functional/UX - P3]
[Mindfulness → Track Mood — +Add entry point doesn't recognize today's already-logged mood]
When a mood has already been logged for today (verified via Diary → Vitals → "Mood: Okay" with an
"Edit mood" button), opening **Track Mood fresh via +Add → Mindfulness** shows a blank/unselected
state with "Save" (disabled) rather than the previously logged mood pre-selected with an "Update"
button. Submitting from this entry point does correctly **overwrite** the existing single per-day
mood record (verified — no duplicate created, Diary correctly reflected the new mood), so there is no
data-integrity issue, but the UI misrepresents state: the user has no indication a mood was already
logged today when using this entry point, and the button is mislabeled "Save" when it will actually
update existing data.
Expected: Both entry points (+Add → Track Mood, and Diary → Edit mood) should show consistent state —
pre-select today's already-logged mood and label the button "Update" if one exists.
Actual: Diary's "Edit mood" correctly pre-fills and labels "Update"; +Add's "Track Mood" always opens
blank with "Save", regardless of whether today's mood is already logged.
Evidence: reproduced directly — see conversation trace (Diary showed "Okay" logged, then +Add → Track
Mood opened with all 5 moods unselected and Save disabled).

## Bug #24 [UI/Responsive - P2]
[Mindfulness → Track Mood — mobile: floating widgets overlap the Save button]
On mobile viewport (390×844) with the Track Mood modal open, bounding-box measurement confirms **both**
the "Chat with us" support widget (box 15,779–160,829) and the floating "+" FAB / "Give recognition"
button (box 169,767–221,819) overlap the modal's "Save" button (box 18,778–372,830) in the same
screen region. This is a z-index/layout conflict between persistent floating UI and modal content on
mobile — the primary CTA of an open modal should never be obstructed by page-level floating widgets.
Expected: Floating widgets (chat, FAB) should be hidden or repositioned while a modal is open, or the
modal should render above them with sufficient bottom clearance.
Actual: Both floating elements sit directly on top of the Save button's active area on mobile.
Evidence: evidence/trackmood_08_mobile.png (bounding boxes captured via accessibility snapshot in the
same session — see conversation trace for exact coordinates).

## Bug #25 [Accessibility - P3]
[Mindfulness → Track Mood — touch target sizes, consistent with prior findings]
Measured via bounding boxes (desktop and mobile):
- Previous/Next day arrow buttons: 29×29px
- Modal Close (X) button: 32×32px
Both fall under the 44×44px minimum, consistent with the same systemic pattern already logged in
Bugs #5/#10/#16 (extended) across other Quick Add surfaces. Mood emoji buttons (57–78px) and factor
tag buttons (113×78px) are both comfortably above the minimum — no issue there.
Evidence: bounding-box data captured via accessibility snapshot, see conversation trace.

---
### Notes / Doubts (not bugs) — Track Mood
- Factor tags ("Exercise", "Education", etc.) are **single-select**, not multi-select — selecting a
  new tag deselects the previous one. This appears to be an intentional design choice (pick the one
  primary reason) rather than a defect.
- Mood-specific follow-up copy is well-written and varies appropriately per mood ("Nice — what made it
  good?", "That sounds rough. Want to note why?", "Off day. What's weighing on you?", "Just okay.
  Anything behind it?", "Superb — anything in particular?") — reviewed for grammar, no issues found.
- Track Mood's date navigation (Previous/Next day arrows, with Next correctly disabled on Today) is a
  **cleaner, more consistent pattern** than Log Activity's horizontal date strip (Bug #16) and correctly
  prevents future-date selection by disabling rather than hiding — this is arguably the pattern Log
  Activity's date picker should adopt for consistency (relevant to Bugs #16/#17).
- Edit/Update flow (via Diary → "Edit mood") works correctly — pre-fills the existing mood, correct
  button label ("Update"), and the change persists correctly on save.

---
## Mindfulness → Log Sleep — findings (AI-tested this session)

## Bug #26 [Accessibility - P2]
[Mindfulness → Log Sleep — Bedtime/Wake up sliders are not keyboard-operable]
The "Sleep timeline" region exposes two `role="slider"` elements (Bedtime, Wake up) with correct
`aria-valuenow`/`aria-valuemin`/`aria-valuemax` attributes. However, after focusing the Bedtime slider
and pressing ArrowLeft twice, **neither the displayed time nor the underlying `aria-valuenow` changed**
(confirmed via direct DOM inspection — value stayed at 180, and the slider wasn't even reported as
`document.activeElement` after the click+keypress sequence). Mouse/pointer **drag** on the same slider
does work correctly (value changed from 180→445, display updated to "1:25 AM", duration recalculated
correctly). This means keyboard-only users and many assistive-technology users cannot adjust sleep
times at all, despite the correct ARIA slider semantics being present.
Expected: Per WAI-ARIA APG slider pattern, Arrow keys (and Home/End/Page Up/Down) should adjust a
focused slider's value.
Actual: Keyboard input has no effect; only pointer drag works.
Evidence: reproduced directly — see conversation trace (click + ArrowLeft×2 → no value change even in
`aria-valuenow`; subsequent `dragTo` action → value changed correctly from 180 to 445).

## Bug #27 [Functional/UX - P3]
[Mindfulness → Log Sleep — +Add entry point doesn't pre-fill today's already-logged sleep]
Same pattern as Bug #23 (Track Mood): after saving sleep data for today (Bedtime 6:00 PM, Wake up
5:00 AM, 8h asleep of 11h in bed — confirmed correctly persisted via Diary and Summary), reopening
"Log Sleep" via **+Add → Mindfulness** shows the **default template** (9:00 PM–5:00 AM, 8h of 8h in
bed) instead of the actual saved values, and the button still reads "Save" rather than "Update". This
confirms the pre-fill/state-representation gap is a **systemic Mindfulness-module pattern**, not
specific to Track Mood.
Expected: Should show today's actual saved sleep data pre-filled, with an "Update" label, consistent
with Diary's behavior for Mood.
Actual: Always opens with the default template regardless of existing data for today.
Evidence: reproduced directly — see conversation trace (saved 6PM–5AM/8h, then reopened via +Add,
showed 9PM–5AM/8h default).

## Bug #28 [UI/Responsive - P2]
[Mindfulness → Log Sleep — mobile: floating widgets overlap the Save button (confirms global issue)]
Identical to Bug #24 (Track Mood): on mobile viewport (390×844), bounding-box measurement confirms
both the "Chat with us" widget (15,779–160,829) and the floating "+"/"Give recognition" FAB
(169,767–221,819) overlap the Log Sleep modal's "Save" button (18,782–372,830). Since this reproduces
identically on a second, unrelated modal, this is confirmed as a **global** layout/z-index issue
affecting any modal with a bottom-anchored primary CTA on mobile, not a Track-Mood-specific bug.
Expected: Floating widgets should never obstruct an open modal's primary action on any screen.
Actual: Both floating elements overlap the Save button here too.
Evidence: evidence/logsleep_03_mobile.png (bounding boxes captured via accessibility snapshot).

## Bug #29 [Accessibility - P3]
[Mindfulness → Log Sleep — touch target sizes]
Measured via bounding boxes:
- Previous/Next day arrow buttons: 29×29px (under 44×44 minimum — consistent with prior findings)
- Modal Close (X) button: 32×32px (under minimum — consistent with prior findings)
- Time-asleep +/- steppers: **48×48px** — meets the minimum, better than the 30×30px steppers found
  in Log Activity (Bug #10) and Track Mood
- Bedtime/Wake up slider handles: 42×42px — just under the 44px minimum, but close; likely acceptable
  given sliders are typically operated via drag across a larger track area, not a single tap target
Expected: All interactive controls ≥44×44px.
Actual: Prev/Next arrows and Close button fall short; steppers and slider handles are close to or
meeting the minimum, an improvement over other Quick Add surfaces.
Evidence: bounding-box data captured via accessibility snapshot, see conversation trace.

---
### Notes / Doubts (not bugs) — Log Sleep
- **Data reflection confirmed correct in two places**: Diary → Sleep card ("8 hrs 0 mins / Total sleep
  duration", replacing the prior "No Data" empty state) and Summary → Trends → "Avg Sleep" ("8 hrs 0
  mins ↑", replacing "0 sec ↓") both updated correctly after a hard reload (same stale-cache-until-
  reload behavior as Bug #8 — data itself is always correct, only the live Summary view needs a reload
  to reflect it).
- **No edit affordance for Sleep in Diary** — unlike Mood's "Edit mood" button, the Diary's Sleep card
  has no visible edit control; clicking it does nothing. This is the same category of gap as Log
  Activity's missing edit/delete (flagged there as a product/scope question, not a bug) — but notably
  **inconsistent with Mood**, which does have full edit support in the same Mindfulness tab. Worth a
  product question: why does Mood get edit support but Sleep doesn't?
- Boundary logic for "time asleep" is correct: floors at 0h 0m (does not go negative), and caps
  correctly at the current "in bed" duration (verified by holding the Increase button past the limit —
  it stopped exactly at the in-bed maximum and the button became disabled).
- "In bed" duration correctly recalculates when Bedtime/Wake up are dragged to new positions (e.g.
  changing Bedtime to 6:00 PM with Wake up at 5:00 AM correctly showed "11h 0m in bed"); the
  previously-set "time asleep" value is preserved (not reset) as long as it still fits within the new
  window — sensible behavior, not a bug.
- Grammar/copy reviewed ("Time asleep", "of Xh Ym in bed", "Bedtime", "Wake up", "Sleep boundaries") —
  no issues found.

---
### Notes / Doubts (not bugs)
- The "Continue this in the Vantage Fit app" modal is fully generic — identical copy and identical QR
  code/download filename (`vantage-fit-qr.png`) regardless of which "Track on app" action was clicked
  (verified for Sync Steps History and Measure Heart Rate). This is a design choice, not a defect —
  flagging as a judgment call: a QA/product opinion could go either way on whether action-specific
  copy would improve clarity, but nothing is functionally wrong.
- "Save QR" button works correctly on both actions tested — triggers a real file download, modal stays
  open afterward (correct, allows re-download). Functionally fine — see Bug #12 for its sizing issue.
- Modal layout (centering, spacing, card styling) reviewed via screenshot on both Sync Steps History
  and Measure Heart Rate — clean and consistent, no overlap or alignment issues found beyond the
  already-logged dropdown-left-open issue (Bug #1).
- Keyboard Tab order for the header cluster (Community → Quick Add → tabs → action rows) is correct
  and matches visual order; a focus ring is visible on the +Add trigger. This rules out a keyboard-trap
  concern for reaching the menu itself — the only keyboard issue found is Bug #3 (modal focus).
- Log Activity → Book Reading, Hiking, Swimming, Bench Press (Strength/Weight Training sub-option),
  Yoga, Post Coffee Walk all saved correctly and are reflected accurately in the Diary (verified via
  Diary's Activities list, Calorie Ledger, and Snapshot Active Minutes — see Bug #8 for the Summary-page
  caveat). Duration/Calories/Reps/Distance fields, unit conversion (km/mi), boundary clamping (calorie
  range cap, 5 min duration floor), and the "Convert to Active Minutes"/"Steps" toggles (including the
  intentionally locked-ON toggle for some activity types, e.g. Swimming) all function correctly.
- Active Minutes progress display correctly caps visually at 100% once logged minutes (165) exceed the
  goal (32 mins) — expected progress-ring/bar behavior, not a bug.
- UI review (layout/spacing/alignment/typography/card styling) across Book Reading, Hiking, and the
  category list found no overlap, misalignment, or inconsistent spacing — cards, section headers,
  and field rows are visually consistent across all sub-forms and viewports. Category list row height
  (54px) and Date/Time row height (48px) both meet the touch-target minimum — only the +/- steppers,
  unit radio buttons, and Close button fall short (Bug #10).
- **Rapid/duplicate submission** — double-clicking "Log activity" (Yoga, via JS-fired double click)
  produced only **one** new Diary entry ("7 logged", up from 6), not two. Submission is correctly
  debounced/guarded against duplicates.
- **Hover states** — verified via before/after screenshot: category rows in the Log Activity list
  (e.g. "Hiking") show a clear gray background highlight on hover, confirming hover states are
  implemented, not just relying on `cursor: pointer`.
- **Disabled states** — the Diary's "Next day" navigation button is correctly disabled when viewing
  today (can't navigate into the future), consistent with the Date picker's past-only date range.
- **No edit/delete for logged activities** — clicking an already-logged activity row in the Diary
  (e.g. "Yoga, 11:25 AM, 45 mins") does nothing; there is no edit or delete affordance found for
  activities once logged. This is a **product/scope gap, not a bug** — flagging as a Note/Doubt
  since it may be an intentional (if inconvenient) design decision for this MVP; worth a product
  question rather than a defect report.

---
## Mindfulness → Guided Meditation — findings (AI-tested this session)

## Bug #30 [Functional/Data - P2]
[Mindfulness → Guided Meditation — completed sessions are not reflected anywhere]
Played a full Guided Meditation session (Yoga Nidra, 10:04 total) to natural completion — seeked the
underlying `<audio>` element to its final ~3 seconds and let the browser's native `ended` event fire
for real, rather than idling the full 10 minutes — and then checked both places sleep/mood data is
known to surface:
- **Summary → Trends → Mindful Minutes**: stayed at "0 sec ↓", unchanged, even after a hard reload
  (ruling out the known stale-cache-until-reload pattern from Bug #8).
- **Diary**: no Meditation/Mindfulness card exists anywhere on the page at all. Snapshot, Calorie
  Ledger, Sleep, Food Log, Intake, Distance, Activities, and Vitals cards are all present and correctly
  populated with the day's other data, but there is no equivalent card for meditation/mindfulness
  minutes.
Expected: Completing a Guided Meditation session should increment "Mindful Minutes" on the Summary
Trends widget (the metric exists and is clearly designed for this) and/or appear in the Diary,
consistent with how Sleep and Mood are both tracked and displayed.
Actual: No visible record of the completed session anywhere in the product.
Note/Doubt: The session was completed via a JS-driven seek-to-end rather than a full real-time
10-minute playthrough. This is a standard, valid QA technique for triggering a genuine `ended` event
without idling, but if the backend's mindfulness-minutes logging depends on elapsed wall-clock/watch-
time heuristics (rather than the `ended` event itself), a real full-duration playthrough should be
re-verified to rule out that specific difference. Recommend a follow-up spot-check with an untouched,
real-time playthrough of a short (5 min) session before treating this as fully confirmed for all
possible logging implementations.
Evidence: evidence/gm_05_after_completion.png, evidence/gm_06_diary.png (Summary Trends and Diary
both captured post-completion, post-hard-reload).

## Bug #31 [UX - P3]
[Mindfulness → Guided Meditation — no completion/success feedback when a session finishes]
When a session's audio reaches its end (`ended` event fires), the "Now playing" player dialog closes
**silently** — no completion screen, toast, congratulatory message, or any other indication that the
session finished successfully, either normally or with the same fanfare a user might expect from
completing an activity elsewhere in the app.
Expected: Some acknowledgment that the session completed — even a simple toast ("Session complete!")
or a brief summary state before the dialog closes.
Actual: Dialog disappears with no visible transition or message; the user is left back on the
Mindfulness library page with no confirmation anything happened.
Evidence: evidence/gm_04_player_paused.png (mid-session) vs. evidence/gm_05_after_completion.png
(post-completion — player gone, no feedback).

## Bug #32 [Accessibility - P3]
[Mindfulness → Guided Meditation — touch target sizes]
Measured via bounding boxes:
- Session detail dialog / player dialog **Close (×) button**: 30×30px — under the 44×44px minimum,
  consistent with the same pattern already logged across every other Quick Add modal (Bugs #5/#10/
  #12/#25/#29).
- Page-level **"Back" button** (top-left, next to the Summary/Challenges/Programs/Community tabs):
  75×29px — height under the 44px minimum, same systemic pattern.
- Player **Rewind 10 seconds / Forward 10 seconds** buttons: 43×43px — borderline, comparable to Log
  Sleep's slider handles (42×42px, judged acceptable there given the surrounding tap area); not filed
  as a separate defect, noted for completeness.
- Player **Pause/Play** button: 58×58px — comfortably meets the minimum.
Expected: All interactive controls ≥44×44px.
Actual: Close (×) and Back fall clearly short; Rewind/Forward are borderline; Pause/Play meets the bar.
Evidence: bounding-box data captured via `getBoundingClientRect()` in an evaluate script, see
conversation trace.

---
### Notes / Doubts (not bugs) — Guided Meditation
- **Player keyboard accessibility is a genuine positive finding**: Tab order through the player is
  correct (Close player → Rewind 10 seconds → Play/Pause → Forward 10 seconds) and pressing Enter on
  the focused Pause/Play button correctly toggled `audio.paused` in both directions. This is notably
  **better** than Log Sleep's Bedtime/Wake up sliders, which are completely non-operable via keyboard
  (Bug #26) despite having correct ARIA slider semantics.
- **Player control accessible names are all correct and descriptive**: "Close player", "Rewind 10
  seconds", "Pause"/"Play", "Forward 10 seconds" — no icon-only, unlabeled controls found in the
  player, unlike the header's overflow icon (Bug #4).
- **Back button navigation verified correct** under a real click-flow (Summary → +Add → Mindfulness →
  Guided Meditation → Back → returns to Summary). An earlier test using direct URL navigation followed
  by Back appeared to go to Diary instead of Summary — this was confirmed to be an artifact of manually
  typing URLs (which alters the real browser history stack), not a product defect, and was excluded
  from the final result.
- **Five session thumbnails initially appeared broken** (5 Senses, Body Scan, 20 Minute Compassion for
  Your Whole Body, 10 Minute Sleep Meditation, 12 Minute Sleep Gratitude Meditation) in a full-page
  screenshot taken immediately after page load. Verified via `naturalWidth`/`complete` checks plus a
  direct `fetch()` of the image URLs (both returned HTTP 200) that this was a **lazy-load timing
  artifact only** — all five images render correctly once scrolled into view. Explicitly not filed as
  a bug; noted here as a demonstration of verifying before reporting per the judgment rules.
- Deep-linking directly to `/ng/fit/mindfulness` works correctly and renders identically to arriving
  via the +Add flow.
- Grammar/copy across the header, subtitle, all session titles and descriptions, and button labels
  ("Start session →", "Begin session", "Now playing") reviewed — no issues found.
- Desktop layout (featured card, 7 category grids, session card typography/spacing) reviewed via
  screenshot — clean, consistent spacing and no overlap issues found.
- Mobile (390×844) landing page reproduces the same global floating-widget overlap already logged as
  Bugs #24/#28 (Chat widget + FAB), confirming it is a page-level issue independent of which Quick Add
  surface is open. The detail dialog and player, however, render cleanly on mobile with **no** overlap,
  since both are centered modals that render above the FAB/chat layer — unlike Track Mood/Log Sleep's
  in-page Save button, which sits within the normal page layout below that layer.

---

## Log Diary → Log Water / Update Weight / Log Meal — findings (AI-tested this session)

## Bug #33 [Accessibility - P2]
[Log Diary → Log Water — modal focus trap]
Opening the Log Water modal (via +Add or via Diary's "Log water" button) does **not** move keyboard
focus into the dialog at all. `document.activeElement` remains `<body>` immediately after open, and
pressing Tab moves focus to the page's overflow-menu icon button in the header, then to the Summary
page's "Open Diary" snapshot card — i.e. the entire underlying page remains fully tabbable while the
modal is visually on top of it.
Expected: Per standard modal/dialog practice (WAI-ARIA dialog pattern), opening a modal should trap
keyboard focus inside it (typically moving focus to the first focusable element or the dialog itself),
and Tab should cycle only within the dialog until it's closed.
Actual: No focus trap exists; a keyboard/screen-reader user can Tab straight past the modal into
background page content while it is open.
Note/Doubt: This is a more severe variant of the pattern behind Bugs #3/#26 (modal keyboard
accessibility issues) — here the modal isn't just missing operable controls, it doesn't capture focus
at all. Worth checking whether Track Mood/Log Sleep/Update Weight modals have the same gap (Update
Weight was spot-checked this session and its slider/stepper controls ARE independently keyboard
operable, but full focus-trap-on-open was not re-verified for it).
Evidence: verified via `document.activeElement` inspection in an evaluate script, see conversation trace.

## Bug #34 [Functional/Data - P2]
[Log Diary → Water — Diary "Intake" card shows wrong unit and wrong goal value]
After logging 750 ml of water (3 glasses) via Log Water, the Diary page's Intake card displays the
Water line as **"25.36/ 2.5 L"** — both numbers are wrong. The numerator (25.36) is actually the
fl-oz-equivalent of 750 ml, mislabeled with an "L" unit; the correct liters value would be 0.75. The
denominator (2.5) doesn't match the 2000 ml (2 L) daily goal shown inside the Log Water modal itself
either. This was verified to persist after a hard reload (not a stale-cache artifact).
Expected: Diary Intake → Water should show "0.75/ 2 L" (or the equivalent in whatever unit the user
last selected), consistently matching the goal shown in the Log Water modal.
Actual: Shows "25.36/ 2.5 L" — an fl-oz numeral with an "L" label, and an unrelated goal denominator.
Evidence: evidence/log-water/lw_02_diary_water_unit_bug.png

## Bug #35 [Functional/Data - P3]
[Summary → Trends — no Water tile exists]
Summary → Trends only shows four tiles (Avg Steps, Active Minutes, Mindful Minutes, Avg Sleep) — there
is no Water/Hydration trend tile at all, even after logging water today. This is a parity gap versus
Sleep and Steps, which do get dedicated Trends tiles despite Mindful Minutes having its own separate
data bug (#30).
Expected: A Water/Hydration trend tile consistent with the other logged metrics, or an explicit product
decision that Water intentionally has no trend (in which case this would not be a bug).
Actual: No such tile exists anywhere in Trends.
Note/Doubt: Filed as P3/Functional rather than a hard blocker since Water data IS visible elsewhere
(Diary Intake card, albeit with the wrong unit per Bug #34) — this is a consistency/completeness gap,
not a total data-loss bug like #30.
Evidence: evidence/log-water/lw_00_modal_initial.png (Summary trends visible in earlier full-page
captures this session show only 4 tiles).

## Bug #36 [Functional/UX - P3]
[Log Diary → Log Water — reopening the modal doesn't reflect today's already-logged total]
After saving 750 ml via Log Water, reopening the modal (either via +Add → Log Diary → Log Water, or
via Diary's "Log water" button) shows **0 ml / 0 of 8 glasses / "2000 ml to goal"** — as if nothing had
been logged today, instead of reflecting the 750 ml already saved (e.g. "750 ml logged, 1250 ml to
goal" or similar). This is the same root-cause pattern as Bugs #23/#27 (Track Mood/Log Sleep +Add entry
points not pre-filling today's already-logged value), now confirmed for Log Water too.
Expected: Reopening Log Water on a day with an existing entry should reflect the running total already
logged (consistent with how Update Weight's Diary "Edit weight" flow correctly shows "Same as last log").
Actual: Always resets to a blank 0 ml / 0 glasses state regardless of what's already logged today.
Evidence: evidence/log-water/lw_03_reopen_no_prefill.png

## Bug #37 [Copy/UI - P4]
[Log Diary → Log Water — "Glasses" sub-label doesn't convert when unit is toggled]
Switching the unit toggle from ml to fl oz correctly converts the main value (e.g. 750 ml → 25 fl oz),
the "to goal" remaining amount, and the ruler tick labels — but the "Glasses" section's sub-label stays
hardcoded as **"1 glass = 250 ml"** even when fl oz is selected (should read "1 glass ≈ 8.5 fl oz" or
similar).
Expected: All unit-dependent copy in the modal should convert together when the toggle changes.
Actual: Only the "Glasses" sub-label is left stale in ml.
Evidence: evidence/log-water/lw_01_floz_unit_glasses_label_bug.png

## Bug #38 [Accessibility - P3]
[Log Diary → Log Water — touch target sizes]
Measured via `getBoundingClientRect()` (desktop and mobile 390×844 — identical results on both):
- Close (×): 32×32px
- Previous/Next day arrows: 29×29px
- Remove/Add a glass (−/+): 34×34px
- ml/fl oz unit toggle segments: 51×31px (height under threshold)
All fall under the 44×44px minimum, consistent with the systemic pattern already logged across every
other Quick Add modal (Bugs #5/#10/#12/#25/#29/#32).
Expected: All interactive controls ≥44×44px.
Actual: All measured controls in this modal fall short except the "Log water" submit button (375×48,
passes).
Evidence: bounding-box data captured via evaluate script, see conversation trace.

## Bug #39 [Accessibility/Functional - P2]
[Mobile (390×844) — bottom-nav "+" FAB has the wrong accessible name]
On mobile, the header's "Quick add" button is not present at all — its mobile equivalent is a red "+"
floating action button in the bottom nav bar. Clicking it correctly opens the same Quick Add sheet
(Workout/Mindfulness/Log Diary/Track Habits, all submodules present) — so it IS functionally the Fit
Quick Add entry point. However, its accessible name/label is **"Give recognition"**, which is an
unrelated Vantage Circle recognition feature, not a description of what it actually does in the Fit
module context.
Expected: The FAB's accessible name should reflect its actual function in this context (e.g. "Quick add"
or "Add entry"), matching the desktop button's "Quick add" label.
Actual: Screen readers and other assistive tech would announce this control as "Give recognition,"
which is actively misleading about what tapping it does inside Vantage Fit.
Note/Doubt: This strongly suggests the bottom nav is a shared cross-product component (reused from the
main Vantage Circle recognition app) that has not been re-labeled for the Fit module context — worth
flagging to design/dev as a shared-component contextualization gap rather than a one-off typo.
Evidence: evidence/log-water/lw_05_mobile_no_quickadd.png (FAB visible), confirmed via
`getByRole('button', { name: 'Give recognition' })` successfully opening the Quick Add sheet.

## Bug #40 [Functional/Data - P2]
[Log Diary → Update Weight — wrong default "latest weigh-in" value before today's first log]
Before any weight has been logged today, opening Update Weight (via +Add, on both desktop and mobile)
shows a default "Your latest weigh-in" value of **165.0 lbs (74.8 kg)** — this does not match the
actual last-known weight shown on Summary → Vitals, which reads **132.28 lbs**. The two values are
unrelated (not a rounding or unit-conversion difference — 132.28 lbs ≈ 60.0 kg, not 74.8 kg).
Expected: The modal's default/starting value should reflect the real last-logged weight (132.28 lbs),
consistent with what Summary → Vitals displays.
Actual: Shows an unrelated, seemingly hardcoded or stale default (165.0 lbs / 74.8 kg) instead.
Note/Doubt: After saving a new weight (164.4 lbs) and reopening via Diary's "Edit weight" button, the
modal correctly showed "Same as last log" with the right value (74.6 kg ≈ 164.4 lbs) — so the bug is
specifically in the **before-any-log-today default**, not in the edit-existing-entry flow. Reopening
via +Add (not Diary edit) after logging today's weight reproduced the same wrong default again (74.8 kg/
165.0 lbs), suggesting +Add's "Update Weight" entry point may not be reading the true last-logged value
at all, regardless of whether an entry exists for today — this compounds with the pattern in Bugs
#23/#27/#36 but is more severe since it shows actively wrong data rather than merely blank/unfilled data.
Evidence: evidence/update-weight/uw_00_prefill_mismatch.png, evidence/update-weight/uw_02_mobile_modal.png

## Bug #41 [Accessibility - P3]
[Log Diary → Update Weight — touch target sizes]
Measured via `getBoundingClientRect()`:
- Close (×): 32×32px
- Previous/Next day arrows: 29×29px
- lbs/kg unit toggle buttons: 29×29px
- Reduce/Increase weight (−/+): **48×48px — passes**
Expected: All interactive controls ≥44×44px.
Actual: Close, day-nav arrows, and unit toggle fall short; Reduce/Increase weight and the Save/Update
weight submit button (375×48) both pass.
Evidence: bounding-box data captured via evaluate script, see conversation trace.

---
### Notes / Doubts (not bugs) — Log Water / Update Weight / Log Meal
- **Log Water's "Any amount" drag-to-fine-tune ruler is completely inaccessible** — it has no `role`,
  no `tabindex`, and its visual caret is `aria-hidden`. It cannot be reached or operated by keyboard at
  all (worse than Log Sleep's Bug #26, which at least had ARIA slider semantics but wasn't operable).
  Not filed as a separate numbered bug since the same functional outcome (adding water) is fully
  achievable via the keyboard-and-mouse-operable Glasses +/− stepper, which was verified to work
  correctly — but flagged here as a real gap for a future accessibility-focused pass.
- **Log Water's over-goal behavior is a genuine positive finding**: adding glasses well past the 8-glass/
  2000 ml goal (tested up to 12 glasses / 3000 ml) is handled gracefully with a "Daily goal reached"
  message and no artificial cap — better than an arbitrary hard limit would be.
- **Log Water's Clear button works correctly**, resetting both the glass count and ruler value to 0.
- **Update Weight's slider IS keyboard-operable** — focusing it and pressing ArrowRight/ArrowLeft moves
  the value in 0.2 lb increments, correctly synced with the numeric display and the +/− stepper buttons.
  This is a genuine positive contrast with Log Sleep's Bug #26 (non-operable sliders).
- **Update Weight has nice contextual copy**: the submit button reads "Save" the first time a weight is
  logged for a day, and "Update weight" when editing an existing entry — a small but well-considered
  detail. Diary's Vitals → Weight row similarly switches from "Log weight" to "Edit weight" once an
  entry exists (mirroring Mood's existing "Edit mood" pattern), unlike Water which always shows
  "Log water" regardless of whether today's entry exists (contributing to Bug #36).
- **Weight data reflection confirmed correct**: after saving 164.4 lbs and a hard reload, both
  Summary → Vitals ("164.4 lbs / Updated on 14 Jul 2026") and Diary → Vitals ("164.4 lbs") updated
  correctly — consistent with the known stale-cache-until-reload behavior already logged for other
  modules (Bug #8 and others).
- **Log Meal reproduces Bugs #1/#2/#3 exactly**: clicking "Log Meal" (labeled "Track on app") opens the
  same "Continue this in the Vantage Fit app" QR modal used by Sync Steps History/Measure Heart Rate.
  Confirmed: (a) the Quick Add dropdown remains visibly open behind/around the modal and stays open
  even after closing the modal via its Close (×) button (Bug #1/#2 pattern); (b) keyboard focus never
  moves into the QR modal — `document.activeElement` stays on the "Log Meal" trigger button, and
  Tab moves to the header's overflow-menu button next, confirming no focus trap (Bug #3 pattern). No
  new bug numbers filed for these — logged here as confirmed reproductions across a third module.

