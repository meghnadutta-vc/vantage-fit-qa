# Functional + UI Test Plan — Vantage Fit Admin (de / es / fr)

**Follows:** VF-2207 localization runs (Runs 1–3). **Driver:** Playwright MCP.
**Goal:** exercise real functionality and UI states per module, and verify that **localized
functional feedback** (validation, toasts, dialogs, empty/search states, date/number formats)
is correct in **German, Spanish, French**.

---

## 1. The 3-language method (non-redundant)

Pure mechanical behavior (does a button work, does a filter filter) is language-independent, so
I do it **once** in **German** (primary). What I re-check in **es + fr** is only the
**language-sensitive functional output**:
- validation / error messages
- success / error **toasts**
- confirmation / warning **dialogs**
- **empty** and **no-results** states (incl. after a gibberish search)
- **date / number** formatting produced by interactions
- **accented-input** handling in search / sort / filter (ä ö ü ñ á é ç à)

So: **behavior once (de); localized-feedback three times (de/es/fr).**

---

## 2. Safety boundary on this LIVE tenant (dashboard-v2.vantagecircle.co.in, ~1003 licences)

| Zone | Actions | What I'll do |
|---|---|---|
| 🟢 **Green — always safe** | navigation, tabs, filters, sort, search, pagination, opening dropdowns/date-pickers/modals, hover/focus, viewport resize, reading validation by leaving fields empty / entering bad data (no submit) | **Do freely** |
| 🟡 **Yellow — creates data** | Create Challenge / Event / Content / Announcement (with `QA-LOC-` prefix), then delete | **Only per your answer below**; if allowed, create → verify → delete, never leave live |
| 🔴 **Red — outward-facing / irreversible** | Publish Notification (in-app to employees), Send Custom Email, Upload Points (distributes real points), Add Employees (adds real users), delete real/existing data, org-wide Settings changes | **Never fire.** I fill + validate the form, then stop before submit |

Validation, search/sort/filter, dialogs, and UI states — the bulk of the localization-relevant
functional value — all live in 🟢 and need no data writes.

---

## 3. Module order & per-module checklist

Order (read-heavy → write-heavy):
1. **Overview** — country filter, date-range picker (presets + custom range), "Ask Vantage Fit" modal (⌘K)
2. **Analyze › Reports** (×6) — filters, column-picker, sort, pagination, export dialog, empty/no-results states
3. **Analyze › Workforce Health** — Wellness Score & Leagues filters, tab/period toggles, export
4. **Challenges** — Active (search/sort/filter/pagination/row actions/view toggle), Past (read), Create Challenge (builder: multi-step nav, validation, char limits)
5. **Engage › Programs** — Content Library (search/filter/view toggle), Create-content modal (Linked Content / Health Bite step flow, validation)
6. **Engage › Community** — Create Event (validation, date/time pickers, FAQ add/remove), View Events (tabs), Create Announcement (form + validation)
7. **Engage › Communications** — Publish Notifications (audience builder, validation, char counters — NO send), Send Custom Email (validation, report-based audience — NO send), Email Designer (step flow)
8. **Manage › Rewards** — Upload Points (wallet/country/type selects, sample download, validation — NO submit)
9. **Manage › Configuration** — Add Employees (template download, validation — NO submit), Preview Emails (preview links open), Settings (toggle behavior — revert immediately; NO permanent change)

### Per-module checklist
**Functional**
- Click every interactive control; confirm the expected state change (re-read after each)
- Forms: required-empty, invalid-format, char-limit, min/max → capture the validation message
- Search / sort / filter, including **accented** query; verify results + no-results state
- Pagination / load-more; tab switches; view (list/grid) toggles
- Date/time pickers: open, pick, verify formatted value
- Dialogs/modals: open + close (Esc + close button)
- 🟡/🔴 writes only per the safety boundary

**UI**
- Layout / overflow / alignment / overlap in each state (screenshot)
- Loading / empty / error / success states
- Hover / focus / active on key controls; keyboard focus order
- Touch-target size on icon buttons; labels/aria on icons
- Responsive: re-check at tablet (~768px) and mobile (~390px) widths on 2–3 representative screens
- Component consistency vs other screens

**Per language (de/es/fr):** re-capture the language-sensitive outputs listed in §1.

---

## 4. Deliverables
- Findings → `bug-logs/bug-log.md`, continuing sequential IDs from **#26**, tagged `[Functional]`
  / `[UI]` / `[Accessibility]`, under a "Functional + UI Pass" run heading. Crashes P1 first.
- Evidence → `evidence/functional/<module>_<state>_<lang>.png` (new subfolder), plus reuse of
  `evidence/{de,es,fr}/` where a language screenshot fits.
- `coverage-log.md` updated per module with functional/UI status + what was NOT exercised (and why).
- Anything blocked (e.g. Health Insights iframe) stays logged BLOCKED, not retried in a loop.

## 5. Explicitly NOT covered
- Real sends / publishes / point uploads / employee adds (🔴) — form + validation only.
- Cross-browser (Chromium/MCP only). Real screen-reader runs. Backend-data correctness.
