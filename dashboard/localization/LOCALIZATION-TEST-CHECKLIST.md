# Localization Test Checklist — Vantage Fit Admin (Frontend, per element)

Execution checklist for Phase 3. **Go module → screen (sub-module) → element**, applying the
relevant checks from the **master checklist** in `LOCALIZATION-TEST-SCOPE.md` (check IDs U1–U10,
F1–F9, A1–A5). Update the **Status** as you test. Element list is from `DASHBOARD-MAP.md`.

**Status:** ☐ To-do · ◐ In progress · ✅ Pass · ❌ Fail (→ bug #) · ⛔ Blocked · ⭕ N/A (backend)
**Language column:** record per language when testing (de / es / fr / pl / ar). Default order:
German-deep first, then Arabic (RTL), then es/fr/pl.

> Each screen has a **(whole screen)** row for cross-cutting checks, then one row per element.
> "Checks" lists the applicable IDs + an element-specific note. `⭕` items are backend (exclude).

---

## Module: Global chrome

### 0. Global chrome (persistent — test once, in each language)
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole chrome) | U1 U2 U3 U4 U6 U9 U10 · F8 · A1 A2 A3 (+U5 Arabic) — all nav/labels translated, no bleed, layout fits, `<html lang>` matches, switch persists | ☐ |
| Product rail (Recognition…Admin Hub) | U1 U9 — product names (shared shell; confirm if in FE scope) | ☐ |
| Profile menu | U1 F1 — opens; items ("HR Admin Dashboard"…) translated | ☐ |
| Create button → global chooser | U1 F1 F5 F9 — opens chooser; options (Challenge/Announcement/Event/Content) translated | ☐ |
| Left-nav items + group headers | U1 F1 F9 — all links/groups translated; expanders open | ☐ |
| Plan badge "Active Plan - Grow" | ⭕ A5 — backend `plan.name` (known-English) | ☐ |
| Language switcher | U1 F1 F8 F9 — switches whole UI; 18 options selectable; persists; **option names not translated (#4, Cat B)** | ☐ |
| Challenges / Licenses counters | U1 U7 — labels translated; numbers formatted | ☐ |
| Contact Account Manager | U1 F1 — label translated; opens | ☐ |

---

## Module: Overview

### 1. Overview — `/fit/overview`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U7 U8 U10 · F8 · A1 A2 A4 · U5 — chrome translated; KPI/skeleton state; date format per locale | ☐ |
| Country filter | U1 F1 F2 F9 — **opens on click + applies** (wire-up #1: "All Countries" shows English though key exists) | ☐ |
| Date-range picker + presets | U1 F1 F2 F9 U7 — opens; presets translated (#2 "This Month"); date value formatted (#3); custom-range-disabled note translated | ☐ |
| KPI cards (Enrolled/Active, Incentivization, Participation) | U1 U7 ⭕ — card labels FE; values backend | ☐ |
| Leadership Insights (AI) | ⭕ A5 — backend/AI text (excluded) | ☐ |
| Org Wellness Score + Score Breakdown | U1 ⭕ — labels FE; scores backend | ☐ |
| At a Glance tiles (Avg Steps/Active Min/Mindful Min/Avg Sleep) | U1 U7 — tile labels + units | ☐ |
| Recommended Actions list | U1 F1 — item labels translated; each deep-links | ☐ |
| Workforce Health Snapshot (Health Status/Top Deficiencies) | U1 ⭕ — labels FE; data backend | ☐ |
| Ask Vantage Fit modal (⌘K) | U1 F1 ⭕ — launcher opens; chrome FE; answers backend/embedded | ☐ |

---

## Module: Challenges

### 2. Create Challenge — `/fit/create-challenge` (+ builder wizard)
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U9 · F8 · A2 · U5 | ☐ |
| Intro heading / "OR" / section heading | U1 U2 — translated; **#7 "…New Challenges" concat capitalisation** | ☐ |
| Template cards (Stress Free Month…) | ⭕ A5 — template names/descriptions backend | ☐ |
| "Use Template" / "Create Challenge" buttons | U1 F1 F9 — translated; open builder | ☐ |
| Challenge-type cards (Custom/Race/Journey/E-Marathon/Streak) + descriptions | U1 F9 — **#6 wire-up: show English though `staticChallenges.*` translated** | ☐ |
| Builder Step 1 (Basic info): Logo, Name, Slogan, About, T&C, Auto-announce toggle | U1 U2 F3 F6 F7 — labels/placeholders/counters translated; validation gating; accented input; Next enables | ☐ |
| Builder Step 2 (Set Duration): start/end date pickers | U1 F1 F7 U7 — date picker opens; DD/MM/YYYY per locale | ☐ |
| Builder later steps (activities/targets → audience → rewards → publish) | U1 U2 F3 F4 F5 F7 — ⚠️ deeper wizard: walk each step in-language; validation/toasts/dialogs localized | ☐ |

### 3. Active Challenges — `/fit/manage-challenge`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U8 U9 · F8 · A2 A5 · U5 | ☐ |
| Title + "Create Challenge" | U1 F1 — translated; opens builder | ☐ |
| Section headers (Ongoing / Upcoming) + counts | U1 — translated | ☐ |
| Challenge card: name/status/type/visibility/participation/dates | U1 U7 ⭕ — visibility ("Private") + "Participation" FE; **status & type backend (#9/#10)**; dates formatted (#11) | ☐ |
| Row actions: View / Manage | U1 F1 F2 — translated; open detail / management view | ☐ |

### 4. Past Challenges — `/fit/past-challenges`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 A5 | ☐ |
| Title + card list (status "Completed", participation, dates) | U1 U7 ⭕ — labels FE; status/names backend; dates formatted | ☐ |
| Row action: View | U1 F1 F2 — opens results detail | ☐ |

---

## Module: Engage

### 5. Content Library — `/fit/programs/on-demand-content`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 A5 · U5 | ☐ |
| Title + "Create" button | U1 F1 — translated; opens chooser | ☐ |
| Search box | U1 F1 F6 — placeholder translated; filters; accented input | ☐ |
| Type / Category filters ("All") | U1 F1 F2 F9 — **wire-up #13 "All" English**; options; applies | ☐ |
| Table headers (Content/Type/Category/Actions) | U1 F9 — headers translated; **#14 Type "Article" wire-up** | ☐ |
| Row: content title / category | ⭕ A5 — backend content data | ☐ |
| Row actions (View content + icon buttons) | U1 F1 U10 — "View content" translated; **icon buttons lack accessible name (a11y)** | ☐ |
| Content Overview panel (Articles/Videos/Podcasts/Bite Size + counts) | U1 U7 — labels translated (loanwords ok) | ☐ |

### 6. Create Content — chooser + Linked Content form / Health Bite builder
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| Create-content chooser modal | U1 F1 F9 — **#12 wire-up: "Create content"/"Linked Content"/"Health Bite" English** | ☐ |
| Linked Content form: Type, Category, Page URL, Image (required), Title (counter) | U1 U2 F3 F6 — labels/placeholders/counter translated; validation (image required); accented title | ☐ |
| Health Bite → Bite-Size builder (`/fit/create-bite-size-content`) | U1 U2 F3 F7 — see `create-content/` test cases; walk builder steps in-language | ☐ |

### 7. Create Event — `/fit/events/create-event`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U9 · F8 · A2 · U5 | ☐ |
| Basic Information (Title, Start/End Date, Start/End Time, Image) | U1 F1 F3 U7 — labels translated; date DD/MM/YYYY + time pickers open; validation | ☐ |
| Target Audience (Country/City/Age Group/Department) | U1 F1 F2 — labels translated; dropdowns open + filter | ☐ |
| Event Details (Venue, About, Benefit +Add more, FAQ +Add more) | U1 F1 — labels/placeholders translated; **dynamic add rows work** | ☐ |
| Send Email Invites toggle | U1 F1 — label translated; toggles | ☐ |
| Reset / Create New Event | U1 F1 F3 F4 — translated; validation-gated; submit + toast localized | ☐ |

### 8. View Events — `/fit/events`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 A5 | ☐ |
| Title + "Create Event" | U1 F1 — translated; opens form | ☐ |
| Tabs (Ongoing/Upcoming/Past Events) | U1 F1 F2 — translated; switch filters list | ☐ |
| Event card (name, dates, invites sent, engagement, "Learn more") | U1 U7 ⭕ — labels FE; name/metrics backend; dates formatted | ☐ |

### 9. Create Announcement — `/fit/community/announcement`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U8 · F8 · A2 · U5 | ☐ |
| List page chrome (Announcements title, banner, Existing Announcements, Search, Show more) | U1 F9 — **#15 not-externalised: entire chrome English (Cat B)** | ☐ |
| Create Announcement button (icon) | U1 F1 U10 — opens form; **no accessible name (a11y)** | ☐ |
| Create form: AI-generate (tone/Generate), Title, Description | U1 U2 F3 — field labels/placeholders translated; header + "Publish" English (#15) | ☐ |
| Publish button | F1 F3 — **#29 functional: never enables with valid fields (repro)** | ☐ |
| Delete (row) → confirm dialog | U1 F1 F4 F5 — **#28 dialog "Are you sure…" English (key exists → wire-up)**; delete works | ☐ |
| Row titles | ⭕ A5 — backend announcement data | ☐ |

### 10. Publish Notifications — `/fit/community/publish-notifications`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | ☐ |
| Notification Content (Title, Subtitle + counters) | U1 U2 F3 F6 — labels/placeholders/counters translated; accented input | ☐ |
| Target Audience (Select Users/Attributes/CSV; filters Dept/Country/Gender/Age) | U1 F1 F2 — modes + filters translated ("Alle …") + apply; Load Employees | ☐ |
| Live Notification Preview | U1 F1 — mirrors typed content; labels translated | ☐ |
| Send Notification | F1 F3 — **#27: enables with only a title (default audience = all) — send-safety** (🔴 don't fire) | ☐ |

### 11. Send Custom Email — `/fit/community/send-custom-email`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | ☐ |
| Email Content (Subject, Headline, Body + counters) | U1 U2 F3 F6 — labels/placeholders/counters translated; accented input | ☐ |
| Target Audience (modes + Build from Report: League/Employee/Participation/Wellness) | U1 F1 F2 — translated; build audience | ☐ |
| Email Preview | U1 — chrome translated; template body may be backend | ☐ |
| "Design a rich email" / Send Email | U1 F1 F4 — opens designer; send (🔴) + toast localized | ☐ |

### 12. Email Designer ("Rich Email Composer") — overlay
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| Composer dialog (title, steps Intro/Write/Design/Send, Start new/Continue, cards, Get started/Import) | U1 F9 — **#16 not-externalised: whole composer English (Cat B)** | ☐ |
| Live preview pane | U1 ⭕ — chrome FE; placeholder copy | ☐ |

---

## Module: Analyze

### 13. Health Insights — `/fit/workforce-health/health-insights` — ⛔ BLOCKED
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| Embedded external dashboard (iframe `dash-vfit…org`) | ⛔ — iframe refused to connect; separate app/own i18n. Re-test where the frame loads | ⛔ |

### 14. Wellness Score — `/fit/workforce-health/wellness-score`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U7 · F8 · A2 A5 · U5 | ☐ |
| Filters (All Countries/Departments/Genders/Age Groups + date) | U1 F1 F2 F9 — **wire-up filter values**; apply | ☐ |
| "Insights" / "AI-generated" | U1 ⭕ — labels FE; insight text backend | ☐ |
| KPI cards, chart titles, legends, segment names | ⭕ A5 — **backend (#17: insights API `header`/`label`)** | ☐ |
| "Employee Wellness Scores" section | ⭕ A5 — backend title/subtitle | ☐ |

### 15. Wellness Leagues — `/fit/workforce-health/wellness-leagues`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 U7 U8 · F8 · A2 A5 · U5 | ☐ |
| Filters + date | U1 F1 F2 F9 — translated; apply | ☐ |
| Current Tier Distribution + "Based on avg daily steps…" | U1 — verify FE vs BE (caption source) | ☐ |
| Tier Trends chart + Weekly/Monthly toggle | U1 F1 F2 — toggle works; period switches | ☐ |
| Columns picker ("Employee ID +8") | U1 F1 F9 — **#18/#20 wire-up: picker labels English vs translated headers** | ☐ |
| Export | U1 F1 — translated; export works | ☐ |
| Table + empty state | U1 U8 ⭕ — headers FE; row data backend | ☐ |

### 16–21. Reports (League / Employee / Participation / Incentivisation / Wellness Score / Redemption)
Shared structure — apply to **each** of the six report screens.
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) ×6 | U1 U2 U3 U4 U6 U7 U8 · F8 · A2 A5 · U5 | ☐ |
| Filter bar (All Countries/Departments/Genders/Age + date, "Enrolled") | U1 F1 F2 F9 — **#19 wire-up filter values**; apply | ☐ |
| Column picker | U1 F1 F9 — **#20 picker names English vs German headers** | ☐ |
| Table column headers | U1 F9 — translated (verify each report's columns) | ☐ |
| Table row data | ⭕ A5 — backend report data | ☐ |
| Export (CSV/Excel) | U1 F1 F2 — menu translated; export works | ☐ |
| Empty / no-data state | U1 U8 — "No data available…" translated | ☐ |
| Wellness Score Report "Employee Wellness Scores" section | ⭕ A5 — **#21 backend title/subtitle** | ☐ |

---

## Module: Manage

### 22. Upload Points — `/fit/reward-hub/upload-points`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | ☐ |
| Select Wallet / Select Country | U1 F1 F2 — labels translated; dropdowns; wallet name ("Reward") ⭕ backend | ☐ |
| Upload Type radios (Primary/Appreciations/Points CSV…) | U1 F1 — labels translated; select | ☐ |
| Download Sample + dropzone | U1 F1 — "Download sample CSV" + dropzone text translated | ☐ |
| Send-email toggle | U1 F1 — label; toggles | ☐ |
| Steps to follow | U1 — all steps translated | ☐ |
| Preview / Submit | U1 F1 F3 F4 — validation-gated; submit (🔴) + toast localized | ☐ |

### 23. Add Employees — `/fit/configuration/add-employees`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | ☐ |
| Title + steps | U1 — translated | ☐ |
| Download template + dropzone | U1 F9 — **#22 not-externalised: "Click to upload or drag and drop" English (Cat B)** | ☐ |
| Note panel (company_id, status rules) + View More | U1 F1 — rules translated; expander works | ☐ |
| Cancel / Preview / Submit | U1 F1 F3 F4 — validation-gated (file required); submit (🔴) + toast | ☐ |

### 24. Preview Emails — `/fit/configuration/preview-emails`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 A5 · U5 | ☐ |
| Title + "N of 9 enabled" | U1 U7 — translated; count formatted | ☐ |
| 9 email-type cards (title + description) | ⭕ A5 — **#23 backend titles/descriptions (email API)** | ☐ |
| "Open in New Tab" links | U1 F1 — translated; opens preview | ☐ |
| Enable/disable toggles + "About Email Settings" | U1 F1 — labels translated; toggle works | ☐ |

### 25. Settings — `/fit/configuration/settings`
| Module / Sub-module / Element | Testing checklist | Status |
|---|---|---|
| (whole screen) | U1 U2 U3 U4 U6 · F8 · A2 · U5 | ☐ |
| Email Settings (banner upload, Challenge completion email, Disable all emails) | U1 F1 — section + toggle labels/descriptions translated | ☐ |
| Challenge Settings (create/update teams, Max team size, team breakdown) | U1 F1 F3 — labels translated; toggles + numeric field | ☐ |
| App Settings (logo upload, multiple-activity save check) | U1 F1 — labels translated; toggle | ☐ |
| (No separate UI-language setting — switcher is the only control) | — | ⭕ |

---

## How to run this checklist
1. Pick a language (start German). 2. Go module → screen top-to-bottom. 3. For each row apply the
listed check IDs (expanded in `LOCALIZATION-TEST-SCOPE.md`). 4. Set **Status** (✅/❌→bug#/⛔/⭕);
log ❌ in `bug-logs/bug-log.md` with the exact string/behaviour + language + evidence. 5. Repeat per
language (add a language tag or duplicate the status cell per language). Backend `⭕` rows are
excluded — only confirm they're the *expected* English, don't log as FE bugs.


