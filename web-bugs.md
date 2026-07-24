# Web Bugs — Consolidated

All Vantage Fit **web** bugs in one place — **excluding Localization, Events, and Announcement** (those
stay in their own logs). Compiled 2026-07-24.

**Sources (full detail + evidence live in each area's log):**
- Create Content — `dashboard/create-content/bug-logs/bug-log.md`
- Diary — `dashboard/diary/bug-logs/bug-log.md`
- Quick Add / Track Habits / Log Diary — `dashboard/quick-add/bug-logs/bug-log.md`
- Summary / Overview — `dashboard/summary/bug-logs/bug-log.md`
- Diary P1 shared report — `shared-reports/2026-07-17-diary-p1-bug-report.md` (re-reports Diary bugs; see Appendix C)

**Excluded (separate reports):** `dashboard/Events/`, `dashboard/announcement/`, `dashboard/localization*/`, `VantageFitWeb/Localization-web/`.

**Active counts:** P1 = 5 · P2 = 25 · P3 = 30 · P4/Enhancement = 6 → **66 active**.
Plus 8 retracted/withdrawn/parked/ignored (Appendix A/B) and 9 duplicate re-reports (Appendix C).
Evidence file names are relative to each source area's `evidence/` folder.

---

## P1 — blockers / data-loss / data-integrity

| Area | ID | Title | Type | FE/BE | Evidence |
|---|---|---|---|---|---|
| Diary | #2 | Sleep card has no edit affordance for an already-logged same-day entry (unlike Weight/Mood) | Functional | - | smoke TC-009b |
| Diary | #3 | Activities card does not update in place after logging (needs hard reload) | Functional | - | API 200 + DOM snapshots |
| Diary | #15 | Log Water fl-oz toggle only partially converts — "1 glass = 250 ml" & "daily max 5L" stay metric | Functional/UI | - | DOM read |
| Diary | #1 | Water intake per-day reset / cross-day misattribution — **materially corrected on re-test**, downgraded to unit-label + goal mismatch; verify current state | Functional/Data | BE (disputed) | diary_01_water_stale_today.png |
| Quick Add | #18 | Future-dated activity logging appears possible — **QA-reported, UNVERIFIED** | Functional | - | - |

---

## P2 — high-impact functional / broken flow / key a11y

| Area | ID | Title | Type | FE/BE | Evidence |
|---|---|---|---|---|---|
| Create Content | #6 | Employee bite-size viewer blank — images 404 (malformed CDN path), X-Frame-Options deny, byteContentId vs biteContentId mismatch (**candidate P1**) | Functional/Backend | BE | 14_employee_bite_view_attempt.png |
| Summary | #2 | Snapshot (Steps/Active Minutes) doesn't refresh in-place after logging (needs navigate away & back) | Functional/Data | - | - |
| Summary | #4 | League banner shows stale "0 active min/day" vs Trends tile "34 mins/day" on the same page | Functional/Data | - | - |
| Diary | #13 | Mobile landscape (844×390) — Chat/Quick-Add pill bar overlaps Snapshot card (~50px) | UI | - | - |
| Diary | #14 | Saving any entry (Mood/Weight/Sleep/Water) gives no confirmation (no toast/snackbar/aria-live) | Accessibility | - | - |
| Quick Add | #3 | "Continue in app" modal opened via keyboard doesn't move focus into the dialog | Accessibility | - | kbd_focus_quickadd.png |
| Quick Add | #7 | Log Activity — "Post Coffee Walk" duplicated 6× under Cardiovascular Activities | Functional/Data | - | logactivity_00_initial_state.png |
| Quick Add | #8 | Summary Snapshot & Trends stay stale after logging an activity; only hard reload refreshes | Functional/Data | - | logactivity_07_hiking_submitted.png |
| Quick Add | #9 | Diary → Activities "View all" opens the Log-Activity add picker instead of the logged-activities list | Functional | - | - |
| Quick Add | #11 | Log Activity sub-form resets to root category list on viewport resize, discarding in-progress data | Functional/UX | - | - |
| Quick Add | #13 | Log Activity Time picker silently clamps future times to now, leaves Duration/Calories stale | Functional/UX | - | - |
| Quick Add | #21 | No "Back" control when closing a directly-opened Workout modal — exits all the way to Summary | Navigation/UX | - | - |
| Quick Add | #22 | Track Mood — Save button color inverted vs sentiment (green=worst, red=best) | UI/UX | - | trackmood_03_horrible_selected.png |
| Quick Add | #24 | Track Mood mobile — Chat widget & "+" FAB overlap the modal's Save button | UI/Responsive | - | trackmood_08_mobile.png |
| Quick Add | #26 | Log Sleep — Bedtime/Wake sliders not keyboard-operable (Arrow keys do nothing) | Accessibility | - | - |
| Quick Add | #28 | Log Sleep mobile — Chat widget & FAB overlap Save button | UI/Responsive | - | logsleep_03_mobile.png |
| Quick Add | #30 | Guided Meditation — completed sessions not reflected anywhere (Mindful Minutes stays 0, no Diary card) | Functional/Data | - | gm_05_after_completion.png |
| Quick Add | #33 | Log Water modal has no focus trap — Tab moves through background page | Accessibility | - | - |
| Quick Add | #34 | Diary Intake shows Water "25.36 / 2.5 L" — wrong unit (fl-oz labeled L) & wrong goal denominator | Functional/Data | - | lw_02_diary_water_unit_bug.png |
| Quick Add | #39 | Mobile bottom-nav "+" FAB has wrong accessible name "Give recognition" (opens Fit Quick Add) | Accessibility/Functional | - | lw_05_mobile_no_quickadd.png |
| Quick Add | #40 | Update Weight prefill shows wrong "latest weigh-in" (165.0 lbs) vs actual 132.28 lbs on Summary | Functional/Data | - | uw_00_prefill_mismatch.png |
| Quick Add | #42 | Log Smoking modal has no focus trap — Tab moves to background | Accessibility | - | smoking_01_modal_open.png |
| Quick Add | #43 | Log Smoking saves on backend (200) but never reflected in web UI (no Vitals row/Trends/pre-fill) | Functional/Data | BE-write OK / FE read missing | smoking_02_no_selected.png |

---

## P3 — UI / UX / visual / minor a11y

| Area | ID | Title | Type | FE/BE | Evidence |
|---|---|---|---|---|---|
| Create Content | #2 | Description required on Save but its label lacks the "*" marker other required fields have | UI/Validation | - | 07_save_validation_errors.png |
| Create Content | #3 | Direct dashboard deep-link silently redirects to employee home (no load/login prompt) | UX | - | - |
| Create Content | #4 | Icon-only row/kebab buttons in Content Library expose an empty accessible name | Accessibility | - | 01_create_content_chooser.png |
| Create Content | #5 | "View content" links accept malformed/non-URL strings; no URL validation (phishing/redirect vector) | Data-validation | - | 01_create_content_chooser.png |
| Diary | #6 | Vitals "Edit" icon (Mood/Weight) renders as a blank circle — SVG has no path data | UI | - | bug_edit_icon_missing.png |
| Diary | #7 | Log Water "of 8 glasses" target never explained as app-set default minimum ("20 of 8 glasses" reads oddly) | Enhancement/Copy | - | - |
| Diary | #16 | Empty states inconsistent across cards — ≥5 different "no data" conventions on one screen | UI/Design-system | - | p3p4_fullpage_desktop.png |
| Summary | #1 | Mobile hamburger "Vantage Points" disclosure uses an unlabeled bare checkbox (no label/aria-expanded) | Accessibility | - | summary_02_mobile_hamburger_menu.png |
| Summary | #3 | Snapshot card has a redundant duplicate "Open Diary" tab stop | Accessibility | - | - |
| Quick Add | #1 | "Continue in app" modal opens but the Quick Add dropdown underneath doesn't close (only Esc closes both) | Functional/UX | - | workout_syncsteps_01_modal.png |
| Quick Add | #2 | Modal Close (X) closes only the modal, leaves the Quick Add dropdown open (inconsistent w/ Esc) | Functional/UX | - | workout_syncsteps_02_modal_viewport.png |
| Quick Add | #4 | +Add header icon buttons unlabeled; mobile FAB mislabeled "Give recognition" | Accessibility | - | quickadd_06_mobile_bottomsheet_mislabel.png |
| Quick Add | #5 | +Add trigger, overflow icon, in-menu tabs, modal Close X all below 44×44 touch-target min | Accessibility | - | - |
| Quick Add | #10 | Log Activity steppers/unit radios/Close (30–34px) below 44×44 min (desktop & mobile) | Accessibility | - | ui_bookreading_desktop.png |
| Quick Add | #12 | "Save QR" primary button undersized (84×27) vs other primary CTAs (~375×50) | UI/Accessibility | - | ui_syncsteps_desktop.png |
| Quick Add | #14 | Log Activity stepper "−/+" glyph contrast 3.07:1 fails WCAG AA (4.5:1) | Accessibility | - | - |
| Quick Add | #16 | Log Activity date picker orientation reversed — today first, past dates to the right (QA-reported) | UX/Consistency | - | - |
| Quick Add | #19 | Log Activity Time picker scroll bleeds into background page scroll (QA-reported, inconclusive) | UX | - | - |
| Quick Add | #23 | Track Mood via +Add doesn't recognize today's already-logged mood (blank + "Save" vs pre-selected + "Update") | Functional/UX | - | - |
| Quick Add | #25 | Track Mood — Prev/Next arrows (29×29) & Close X (32×32) below touch-target min | Accessibility | - | - |
| Quick Add | #27 | Log Sleep via +Add doesn't pre-fill today's already-logged sleep | Functional/UX | - | - |
| Quick Add | #29 | Log Sleep — Prev/Next arrows & Close X below touch-target min | Accessibility | - | - |
| Quick Add | #31 | Guided Meditation — no completion/success feedback when a session finishes | UX | - | gm_05_after_completion.png |
| Quick Add | #32 | Guided Meditation — Close (30×30) & Back (75×29) below touch-target min | Accessibility | - | - |
| Quick Add | #35 | Summary → Trends has no Water/Hydration tile (parity gap vs Sleep/Steps) | Functional | - | lw_00_modal_initial.png |
| Quick Add | #36 | Reopening Log Water doesn't reflect today's already-logged total (resets to 0) | Functional/UX | - | lw_03_reopen_no_prefill.png |
| Quick Add | #38 | Log Water — Close/day-arrows/glass-steppers/unit-toggle all below 44×44 min | Accessibility | - | - |
| Quick Add | #41 | Update Weight — Close/day-arrows/unit-toggle (29–32px) below touch-target min | Accessibility | - | - |
| Quick Add | #44 | Log Smoking — Close/day-arrow below min; Save 375×43 (1px short) | Accessibility | - | - |
| Quick Add | #45 | Log Smoking mobile — "Chat with us" widget overlaps the bottom-sheet Save button | UI/UX | - | smoking_04_mobile_modal.png |

---

## P4 / Enhancement

| Area | ID | Title | Type | FE/BE | Evidence |
|---|---|---|---|---|---|
| Create Content | #7 | Preview caption says "text shown is placeholder copy" while preview renders real authored content | Copy/UX | - | 13_preview_with_real_content.png |
| Diary | #5 | Weight display disagrees across Diary/Summary/Log-Weight modal (two endpoints, different "current weight" semantics) | Enhancement | BE | app/home vs today/overview API |
| Quick Add | #6 | "Continue in app" modal disappears silently on viewport resize | UI/State | - | workout_syncsteps_03_modal_mobile.png |
| Quick Add | #15 | Grammar — comma splice in email-verification banner; dropped preposition in Snapshot subtext | Copy | - | - |
| Quick Add | #17 | Log Activity date picker omits future dates entirely instead of greying them (QA-reported) | UX/Consistency | - | - |
| Quick Add | #20 | Post-save redirect lands on Summary instead of showing the just-logged activity (QA-reported) | UX | - | - |

---

## Appendix A — Retracted / Withdrawn / Invalid (not active bugs)
- **Create Content #1** — RETRACTED (invalid): preview claimed placeholder-only, actually renders authored content.
- **Diary #4** — RETRACTED: Distance card is app-only by design ("Start Outdoor Workout — Track on app").
- **Diary #9** — WITHDRAWN: Distance empty "—" rows deemed acceptable empty-state convention.
- **Diary #11** — IGNORED: active nav tab visual-only, no aria-current.
- **Diary #12** — IGNORED: Freshchat "Chat with us" widget doesn't close on Escape (third-party).

## Appendix B — Parked (a11y, reproduce on request)
- **Diary #8 (PARKED-KF-1)** — Log Water & Log Weight modals don't trap keyboard focus.
- **Diary #10 (PARKED-KF-2)** — Log Mood modal: keyboard focus never enters the modal.

## Appendix C — Diary P1 shared report (duplicate re-reports)
These re-report bugs already listed above; kept for traceability of `shared-reports/2026-07-17-diary-p1-bug-report.md`:
| Report ID | Maps to |
|---|---|
| BUG-01 | Diary #34/#15/#1 (water unit mismatch) |
| BUG-02 | Diary #34 / #1 re-test (water goal 2000 ml vs 2.5 L) |
| BUG-03 | Diary #2 (sleep not editable) |
| BUG-04 | Diary #3 / Summary #2 (no in-place refresh) |
| BUG-05 | Diary #5 (weight inconsistent across views) |
| BUG-06 | Diary a11y (no visible keyboard focus indicator) — P2 |
| BUG-07 | Diary a11y ("More" overflow button no accessible name) — P3 |
| BUG-08 | Diary a11y (no "Skip to main content") — P4 |
| BUG-09 | Diary a11y (footer heading hierarchy H2→H4) — P4 |

*(BUG-06 through BUG-09 are the only ones not already itemized above; treat as Diary accessibility findings.)*
