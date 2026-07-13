# Vantage Fit Admin — Dashboard Map (element + functional-flow inventory)

**Purpose:** an exhaustive, observation-grounded catalogue of every module → screen → UI element →
functional flow in the Vantage Fit **admin** module. Built as the ground truth for localization
testing (Phase 1 of 3: map → identify frontend strings → test each string/flow per language).

**Baseline language:** English. **Driver:** Playwright MCP (Chromium).
**URL:** `https://dashboard-v2.vantagecircle.co.in/fit/overview` (India `.co.in` instance).
**Access:** employee app → profile menu → **HR Admin Dashboard** (token handshake, new tab) →
navigate to `/fit/*`. Direct deep-links bounce to the employee app. UAT account, admin, Plan: Grow.

**Method (anti-hallucination):** one screen at a time; each entry is built from a fresh
accessibility snapshot + screenshot; labels quoted exactly; interactive elements opened to reveal
hidden content; written immediately and its tracker row flipped. No destructive writes during
documentation (behaviour is described, not executed).

**Legend:** ☐ todo · ◐ in progress · ✅ done · ⛔ blocked

---

## Progress tracker

| # | Group | Screen | Route | Status |
|---|---|---|---|---|
| 0 | — | Global chrome | (all screens) | ✅ |
| 1 | — | Overview | `/fit/overview` | ✅ |
| 2 | Challenges | Create Challenge | `/fit/create-challenge` | ◐ (landing+steps 1–2; deeper wizard pending) |
| 3 | Challenges | Active Challenges | `/fit/manage-challenge` | ✅ |
| 4 | Challenges | Past Challenges | `/fit/past-challenges` | ✅ |
| 5 | Engage · Programs | Content Library | `/fit/programs/on-demand-content` | ✅ |
| 6 | Engage · Programs | Create Content (chooser + forms/builders) | `…?action=create` / `/fit/create-bite-size-content` | ✅ |
| 7 | Engage · Community | Create Event | `/fit/events/create-event` | ✅ |
| 8 | Engage · Community | View Events | `/fit/events` | ✅ |
| 9 | Engage · Community | Create Announcement | `/fit/community/announcement` | ✅ |
| 10 | Engage · Communications | Publish Notifications | `/fit/community/publish-notifications` | ✅ |
| 11 | Engage · Communications | Send Custom Email | `/fit/community/send-custom-email` | ✅ |
| 12 | Engage · Communications | Email Designer | (overlay) | ✅ |
| 13 | Analyze · Workforce Health | Health Insights | `/fit/workforce-health/health-insights` | ⛔ (iframe refused to connect) |
| 14 | Analyze · Workforce Health | Wellness Score | `/fit/workforce-health/wellness-score` | ✅ |
| 15 | Analyze · Workforce Health | Wellness Leagues | `/fit/workforce-health/wellness-leagues` | ✅ |
| 16 | Analyze · Reports | League Report | `/fit/leagues` | ✅ (empty-state; shared report structure) |
| 17 | Analyze · Reports | Employee Report | `/fit/employee-report` | ✅ |
| 18 | Analyze · Reports | Participation Report | `/fit/participant-report` | ✅ |
| 19 | Analyze · Reports | Incentivisation Report | `/fit/transaction-report` | ✅ |
| 20 | Analyze · Reports | Wellness Score Report | `/fit/wellness-score-report` | ✅ |
| 21 | Analyze · Reports | Redemption Report | `/fit/redemption-report` | ✅ |
| 22 | Manage · Rewards | Upload Points | `/fit/reward-hub/upload-points` | ✅ |
| 23 | Manage · Configuration | Add Employees | `/fit/configuration/add-employees` | ✅ |
| 24 | Manage · Configuration | Preview Emails | `/fit/configuration/preview-emails` | ✅ |
| 25 | Manage · Configuration | Settings | `/fit/configuration/settings` | ✅ |

---

## 0. Global chrome (persistent across all screens)

**Layout regions:** (1) product rail — far-left icon column; (2) top banner; (3) left sub-nav +
bottom widget stack; (4) main content area (per-screen).

### 0.1 Product rail (VC product switcher — left icon column)
| Element | Type | Exact label | Function / on-interact | Notes |
|---|---|---|---|---|
| Collapse sidebar | button | "Collapse sidebar" | Collapses/expands the product rail | |
| Recognition | button | "Vantage Recognition" | Switches to Recognition product | Out of Fit scope |
| Wellness (current) | button | "Vantage Fit" (labelled "Wellness") | Current product — the Fit admin | |
| Pulse | button | "Vantage Pulse" | Switches to Pulse | Out of scope |
| Redemption | button | "Redeem" (labelled "Redemption") | Switches to Redemption | Out of scope |
| Milestone | button | "Service Milestone" (labelled "Milestone") | Switches to Milestone | Out of scope |
| Perks | button | "Vantage Perks" (labelled "Perks") | Switches to Perks | Out of scope |
| Admin Hub | button | "Overall" (labelled "Admin Hub") | Switches to Admin Hub | Out of scope |

### 0.2 Top banner
| Element | Type | Exact label | Function / on-interact |
|---|---|---|---|
| Company logo | img | "Company Logo" | Brand; (no observed action) |
| Profile menu | button | "Open profile menu" | Opens profile menu → View Profile, Change City, **HR Admin Dashboard**, Points Transfer, Site Admin, Add to Slack, Site Tour, FAQ, Sign Out (this is the admin-access entry point) |

### 0.3 Left sub-nav (Fit module)
Group headers are non-clickable labels; expandable sections (Programs, Community, Communications,
Workforce Health, Reports, Rewards, Configuration) toggle open/closed to reveal their links.

| Item | Type | Exact label | Route / action |
|---|---|---|---|
| Create | button | "Create" | Opens global **create chooser** modal (see Overview §1 overlay) |
| Overview | link | "Overview" | `/fit/overview` |
| — group — | label | "Challenges" | — |
| Create Challenge | link | "Create Challenge" | `/fit/create-challenge` |
| Active Challenges | link | "Active Challenges" + count badge ("73") | `/fit/manage-challenge` |
| Past Challenges | link | "Past Challenges" | `/fit/past-challenges` |
| — group — | label | "Engage" | — |
| Programs | button (expander) | "Programs" | → Create Content (`…?action=create`), Content Library (`/fit/programs/on-demand-content`) + "FREE" badge |
| Community | button (expander) | "Community" | → Create Event (`/fit/events/create-event`), View Events (`/fit/events`), Create Announcement (`/fit/community/announcement`) |
| Communications | button (expander) | "Communications" | → Publish Notifications (`/fit/community/publish-notifications`), Send Custom Email (`/fit/community/send-custom-email`), Email Designer (overlay) |
| — group — | label | "Analyze" | — |
| Workforce Health | button (expander) | "Workforce Health" | → Health Insights (`/fit/workforce-health/health-insights`), Wellness Score (`/fit/workforce-health/wellness-score`) + "NEW" badge, Wellness Leagues (`/fit/workforce-health/wellness-leagues`) |
| Reports | button (expander) | "Reports" | → League (`/fit/leagues`), Employee (`/fit/employee-report`), Participation (`/fit/participant-report`), Incentivisation (`/fit/transaction-report`), Wellness Score (`/fit/wellness-score-report`), Redemption (`/fit/redemption-report`) |
| — group — | label | "Manage" | — |
| Rewards | button (expander) | "Rewards" | → Upload Points (`/fit/reward-hub/upload-points`) |
| Configuration | button (expander) | "Configuration" | → Add Employees (`/fit/configuration/add-employees`), Preview Emails (`/fit/configuration/preview-emails`), Settings (`/fit/configuration/settings`) |

### 0.4 Left-rail bottom widgets
| Element | Type | Exact label | Function / on-interact |
|---|---|---|---|
| Plan badge | badge | "Active Plan - Grow" | Shows current plan (value from backend `config.plan.name`) |
| Language label | text | "Language" | Label above the switcher |
| Language switcher | combobox | "Content language" | Selecting re-renders the whole admin UI; persists via `localStorage.fit_lang`. **18 options:** English, Arabic, Chinese (Simplified), Dutch, French, French (Canada), German, Italian, Korean, Portuguese, Russian, Spanish, Vietnamese, Indonesian, Polish, Hungarian, Hindi, Odia |
| Challenges counter | stat | "Challenges" + "1510/∞" | Usage counter (quota) |
| Licenses counter | stat | "Licenses" + "1004" | Usage counter |
| Contact Account Manager | button | "Contact Account Manager" | Opens contact/support action |

### Functional flows (chrome)
1. **Admin access:** Profile menu → "HR Admin Dashboard" → token handshake opens `dashboard-v2` → `/fit/*`.
2. **Create (global):** top "Create" button → chooser modal (Challenge / Announcement / Event / Content).
3. **Nav expanders:** clicking a group (e.g. "Programs") expands/collapses its sub-links.
4. **Language switch:** combobox → pick language → whole UI re-renders; preference persists on reload.

**Badges:** "FREE" (Content Library nav), "NEW" (Wellness Score nav).
**Evidence:** evidence/en/overview_landing.png (chrome visible on every screen).

## 1. Overview — `/fit/overview`
**Access:** left nav → "Overview" (default landing). **Purpose:** org-wide wellness dashboard —
KPIs, AI insights, and recommended actions. **Layout regions:** filter bar (top) · KPI card row ·
Leadership Insights (AI) · Org Wellness Score + At a Glance + Recommended Actions · Workforce Health
Snapshot · floating "Ask Vantage Fit" launcher.

### Elements
| Element | Type | Exact label/text | Function / on-interact | States | Notes |
|---|---|---|---|---|---|
| Country filter | dropdown | "All Countries" | Opens single-select country list; filters all dashboard data by country | default = All Countries | See overlay 1a |
| Date-range | dropdown | "This Month" + "Jul 01, 2026 - Jul 12, 2026" | Opens preset list + calendar; changes the reporting period | default = This Month | See overlay 1b; **custom range disabled here** |
| KPI: Enrolled/Active Users | stat card | "Enrolled Users" / "Active Users" + count ("1K +401") + "View more →" | "View more" drills into detail | loads async (can skeleton — O1) | |
| KPI: Incentivization | stat card | "Incentivization" + "£0" + "vs Prev Quarter" + "View more →" | drill-in | | currency value |
| KPI: Participation Rate | stat card | "Participation Rate" + "0%" + "vs Prev period" | — | | |
| Leadership Insights | panel | "Leadership Insights" + "AI-generated" | AI-written summary | | content from AI/BE |
| Org Wellness Score | stat + chart | "Org Wellness Score" + "43" + "vs Prev Quarter" + "SCORE BREAKDOWN" (Health Baselines 20%, Participation 30%…) + "View more →" | drill-in | | |
| At a Glance | stat group | "At a Glance" + "This Month" + tiles: "Avg Steps", "Active Minutes", "Mindful Minutes", "Avg Sleep" (each with /day + %) | — | | |
| Recommended Actions | action list | "Recommended Actions" / "System suggested next steps" — items: "View Inactive Employees" (e.g. "51 users inactive for 30+ days"), "Nudge Inactive Users", "Review Org Wellness Score", "Check Health Insights", "View Wellness Leagues", "View Employee Report", "Add New Content", "Post an Announcement", "Add Employees", "Upload Bonus Points" | each item links to its screen/action | | deep-links to other modules |
| Workforce Health Snapshot | panel | "Workforce Health Snapshot" / "Aggregated insights only" + "HEALTH STATUS" (Normal / Needs Attention) + "TOP DEFICIENCIES" + "View Insights →" | drill-in | | |
| Ask Vantage Fit | launcher | "Ask Vantage Fit anything" + "⌘K" | Opens AI chat panel | | See overlay 1c |

### Overlay 1a — Country filter dropdown
Single-select scrollable list: "All Countries", then countries (Australia, Bhutan, Bolivia, Canada,
India, Italy, Libya, …). Selecting filters the dashboard. (Country list = backend data.)
Evidence: evidence/map/01_overview_country-filter.png

### Overlay 1b — Date-range picker
Left: preset list — "This Month", "Last Month", "Last 30 Days", "This Quarter", "Last Quarter",
"Last 90 Days", "This Year", "Last year". Right: two-month calendar (day headers Su–Sa) + two date
fields + "Cancel" / "Apply". **Note shown:** "Custom date range is not available for this page.
Please select a preset." (custom range disabled on Overview). Evidence: evidence/map/01_overview_daterange.png

### Overlay 1c — Ask Vantage Fit (AI chat panel)
Right-side panel: title "Ask Vantage Fit" / "Grounded in your org data"; greeting "Hi there — ask me
anything about Vantage Fit."; subtitle "Answers come straight from your governed wellness data —
never invented."; 4 suggestion chips ("How is my organisation performing overall?", "How many
employees are actively participating?", "What does our workforce health snapshot look like?", "What
does the overview dashboard show?"); text input; send button; footer "Grounded in your Vantage Fit
data — nothing invented" / "↵ to send"; header controls (collapse, new/edit, close). Opens via
launcher or ⌘K. (Separate embedded widget.) Evidence: evidence/map/01_overview_ask-vantage-fit.png

### Functional flows
1. **Filter:** choose country and/or date preset → all KPIs/charts recompute for that scope.
2. **Drill-in:** each "View more →" / "View Insights →" navigates to the detailed screen.
3. **Recommended Actions:** each row deep-links to the relevant module (e.g. "Add Employees" → Configuration).
4. **Ask Vantage Fit:** launcher or ⌘K opens the AI chat; typing a question / clicking a chip queries org data.

### States
default (populated) · KPI cards load async and can show **skeleton loaders** (observation O1: a
pre-existing `disableRange` date-picker JS error intermittently blocks the body from rendering).

**Evidence:** evidence/map/01_overview.png

## 2. Create Challenge — `/fit/create-challenge`
**Access:** left nav → Challenges → "Create Challenge" (or top "Create" → Challenge).
**Purpose:** start a new challenge from a template or from a challenge-type builder.
**Layout regions:** intro heading · pre-built template cards · "OR" divider · challenge-type cards.

### Elements — landing
| Element | Type | Exact label/text | Function / on-interact | Notes |
|---|---|---|---|---|
| Intro | heading | "Start creating Challenges using our pre-built templates" / "with no configuration hassles" | — | |
| Template card ×4 | card | "Stress Free Month", "Elevate Endurance", "Mindful Moving", "Healthy Habits Hero" + descriptions + "NEW" badge | "Use Template" button → opens builder pre-filled | template names/descriptions = backend content |
| Use Template | button | "Use Template" | Launches builder from that template | |
| Divider | text | "OR" | — | |
| Section heading | text | "Create your own New Challenges" | — | ("New" concatenation, see bug #7) |
| Challenge-type card ×5 | card | "Custom Challenge", "Race Challenge", "Journey Challenge", "E-Marathon", "Streak Challenge" + descriptions | "Create Challenge" button → opens that type's builder | |
| Create Challenge | button (×5) | "Create Challenge" | Opens the builder for the chosen type | |

### Functional flow — Custom Challenge builder (multi-step wizard) `…/custom-challenge`
A step-based wizard. **Verified steps:**
- **Step 1 — Basic info** (`/custom-challenge`): "Creating Custom Challenge" heading; "Back" button;
  **Challenge Logo** (radio: "Default Image 01", "Default Image 02"; "Upload from System" button);
  **Challenge Name*** (textbox, placeholder "This will appear on the app"); **Challenge Slogan**
  (placeholder "This will appear on contest emails"); **About the Challenge** (textarea, "Maximum
  2000 characters allowed"); **Terms & Conditions** (textarea, "Maximum 1500 characters allowed");
  **Auto-Announce Winners on Social Feed** toggle + consent ("I agree to auto-generate a social feed
  post after the challenge ends, announcing and tagging the winners."); **"Next"** (disabled until
  Challenge Name filled — validation gating confirmed).
- **Step 2 — Set Duration** (`/custom-challenge/challenge-duration`): "Set Duration" heading;
  **Challenge Start Date** (date field, format "DD/MM/YYYY", opens a calendar); "Back"/"Next" (Next
  gated until a valid date range is chosen via the calendar).
- **Further steps (NOT yet walked — flagged, not fabricated):** after duration the wizard continues
  to activity/target configuration, audience selection, and rewards before a final publish step.
  These require calendar + multi-select interaction and were not completed in this pass. ⚠️ **To be
  walked in a dedicated wizard pass.**
- The other four types (Race, Journey, E-Marathon, Streak) open analogous builders with
  format-specific config — not individually walked; structure assumed similar, to verify later.

### States
Builder validation gates each step (Next disabled until required fields valid). Accented input accepted.

**Evidence:** evidence/map/02_create-challenge_landing.png · evidence/map/02_create-challenge_builder-step1.png
⚠️ **Status: partially walked** — landing + builder steps 1–2 verified; deeper wizard steps pending.

## 3. Active Challenges — `/fit/manage-challenge`
**Access:** left nav → Challenges → "Active Challenges" (count badge). **Purpose:** manage ongoing &
upcoming challenges. **Layout regions:** header (title + Create button) · two card columns.

### Elements
| Element | Type | Exact label/text | Function / on-interact | Notes |
|---|---|---|---|---|
| Title | heading | "Active Challenges" / "Manage your ongoing and upcoming wellness challenges" | — | |
| Create Challenge | button | "Create Challenge" | → `/fit/create-challenge` | |
| Section: Ongoing | column | "Ongoing Challenges" (+ count) | lists live challenges | |
| Section: Upcoming | column | "Upcoming Challenges" (+ count) | lists scheduled challenges | |
| Challenge card | card | challenge name (e.g. "Badge Award") + status ("Ends in 3 days" / "Ended" / "Coming Soon") + type ("Multi Week Multi Activity") + visibility ("Private") + "Participation" % + participants count + date range | — | name/status/type = backend data |
| View | button (per card) | "View" | Opens read-only challenge detail | |
| Manage | button (per card) | "Manage" | Opens challenge management view (edit/monitor) | deeper sub-screen — not deep-walked |

### Functional flows
1. Scroll the two columns; each card links to **View** (detail) or **Manage** (management).
2. "Create Challenge" → builder (screen 2).

### States
Populated (two columns of cards). Empty state per section when none. No search/sort/filter controls
present on this screen.

**Evidence:** evidence/map/03_active-challenges.png

## 4. Past Challenges — `/fit/past-challenges`
**Access:** left nav → Challenges → "Past Challenges". **Purpose:** review completed challenges +
performance. **Layout regions:** header · card list.

### Elements
| Element | Type | Exact label/text | Function / on-interact | Notes |
|---|---|---|---|---|
| Title | heading | "Past Challenges" / "Review completed challenges and their performance metrics" | — | |
| Create Challenge | button | "Create Challenge" | → builder | |
| Challenge card | card | name + status "Completed" + type + "Private" + "Participation" % (0/25/50/61/100%) + participants + date range | — | data = backend |
| View | button (per card) | "View" | Opens read-only challenge detail/results | (no "Manage" on past challenges) |

### Functional flows
1. Browse completed challenges; "View" opens each one's results detail.

### States
Populated card list; empty state when none.

**Evidence:** evidence/map/04_past-challenges.png

## 5. Content Library — `/fit/programs/on-demand-content`
**Access:** left nav → Engage → Programs → "Content Library" (+ FREE badge). **Purpose:** manage &
curate on-demand content. **Layout regions:** header · toolbar (search + filters + Create) · content
table · right "Content Overview" summary panel.

### Elements
| Element | Type | Exact label/text | Function / on-interact | Notes |
|---|---|---|---|---|
| Title | heading | "Content Library" / "Manage content availability and curate featured items." | — | |
| Create | button | "Create" | Opens content create-chooser (screen 6) | |
| Search | textbox | "Search content..." | Filters the table by title (verified: 25→4 for "meditation") | |
| Type filter | dropdown | "All" | Filters by content type | default All |
| Category filter | dropdown | "All" | Filters by category | default All |
| Table | table | columns: "Content", "Type", "Category", "Actions" | lists content rows | |
| Row: Content | cell | title + tagline (content data) | — | BE data |
| Row: Type | cell | "Article" / "Video" / etc. | FE-mapped from enum | |
| Row: Category | cell | e.g. "Mindfulness", "Exercise" | BE data | |
| Row actions | icon buttons + link | "View content" link + unlabeled icon buttons (edit/toggle/delete) | per-row actions | ⚠️ icon buttons have no accessible name (a11y) |
| Content Overview | side panel | "Content Overview" + counts "Articles", "Videos", "Podcasts", "Bite Size" | summary of library by type | |

### Functional flows
1. **Search** filters rows live. 2. **Type/Category filters** narrow the table. 3. **Create** →
create-chooser. 4. Row **View content** opens the item; icon buttons edit/toggle-availability/delete.

### States
Populated table + list/grid view toggle; no-results state on search miss.

**Evidence:** evidence/map/05_content-library.png

## 6. Create Content — `…?action=create` (chooser) → Linked Content form / Health Bite builder
**Access:** Content Library "Create" button, or nav "Create Content", or global Create → Content.
**Purpose:** add a new content item. **Layout:** a small chooser modal → then a form/builder.

### Overlay 6a — Create-content chooser (modal)
| Element | Type | Exact label/text | Function |
|---|---|---|---|
| Title | heading | "Create content" / "What would you like to create?" | — |
| Linked Content | option button | "Linked Content" / "Add an article, video or podcast link." | → Linked Content form (6b) |
| Health Bite | option button | "Health Bite" / "Author a bite-size content experience." | → Bite-Size content builder (6c) |
| Close | button | (X) | closes modal |
Evidence: evidence/map/06_create-content-chooser.png

### Form 6b — Linked Content ("Create Linked Content", verified this session)
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Type | dropdown | "Type" → options "Article", "Video", "Podcast" | content type |
| Category | dropdown | "Category" (e.g. Mindfulness) | content category |
| Page URL | textbox* | "Page URL" placeholder "https://" | required |
| Image | upload* | "Image" + "Upload from System" | **required** (blocks save without an image) |
| Title | textbox* | "Title" placeholder "Enter your title here..." + char counter ("… characters left", 150 cap) | required; counter decrements |
| (submit) | button | (create/publish) | saves the content item |

### Builder 6c — Health Bite / Bite-Size content builder — `/fit/create-bite-size-content`
Multi-step authoring builder (VF-2126). Documented in detail in
`dashboard/create-content/test-cases/bite-size-content-builder.md` (Languages step; Intro / Content
/ Quiz page types; per-page-type widgets). **Cross-referenced, not re-inventoried here.**

### Functional flows
1. Create → chooser → pick Linked Content or Health Bite.
2. Linked Content: fill Type/Category/URL/Image/Title → save (image required).
3. Health Bite: opens the Bite-Size builder wizard.

**Evidence:** evidence/map/06_create-content-chooser.png (+ see create-content docs for the Bite-Size builder)

## 7. Create Event — `/fit/events/create-event`
**Access:** left nav → Engage → Community → "Create Event" (or global Create → Event).
**Purpose:** schedule a wellness event. **Layout regions:** "Basic Information" · "Target Audience" ·
"Event Details" · send-invites toggle · action buttons. Single long form (not a wizard).

### Elements
| Section | Element | Type | Exact label/text | Function / notes |
|---|---|---|---|---|
| Basic Information | Event Title | textbox | "Event Title" | required |
| | Event Start/End Date | date | "Event Start Date" / "Event End Date" ("DD/MM/YYYY") | opens calendar |
| | Event Start/End Time | time picker (button) | "Event Start Time" / "Event End Time" | opens time picker |
| | Event Image | upload | "Event Image" + "Upload from System" | |
| Target Audience | Country / City / Age Group / Department | dropdowns | "Country", "City", "Age Group", "Department" | scope the invite audience |
| Event Details | Event Venue | textbox | "Event Venue" | |
| | About this Event | textarea | "About this Event" | |
| | Benefit | repeatable | "Benefit of this Event" (placeholder "Write your benefit here") + "Add more benefits" | **dynamic add** — adds another benefit field |
| | FAQ | repeatable | "FAQ" (placeholder "Write your question here" / "Write your answer here") + "Add more FAQ" | **dynamic add** (verified 1→2) |
| — | Send Email Invites | switch | "Send Email Invites to join this Event" | toggles emailing invites on create |
| — | Reset | button | "Reset" | clears form |
| — | Create New Event | button | "Create New Event" | submits (disabled until required valid) |

### Functional flows
1. Fill required fields → "Create New Event" enables → submit creates the event (optionally emails invites).
2. "Add more benefits" / "Add more FAQ" append repeatable field groups. "Reset" clears the form.

### States
Validation-gated submit; dynamic add/remove of benefit/FAQ rows.

**Evidence:** evidence/map/07_create-event.png

## 8. View Events — `/fit/events`
**Access:** left nav → Engage → Community → "View Events". **Purpose:** browse/manage/analyze
events. **Layout regions:** header (+ Create Event) · tab bar · event card list.

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | heading | "View Events" / "Create, manage, and analyze wellness events" | — |
| Create Event | button | "Create Event" | → `/fit/events/create-event` |
| Tabs | tablist | "Ongoing Events", "Upcoming Events", "Past Events" | switches the event list by status |
| Event card | card | event name (e.g. "New Event") + date range + "No of invites sent" (count) + "User Engagement" (% + "N of N participants active") + "Learn more →" | metrics per event |
| Learn more | link | "Learn more →" | opens event detail/analytics |

### Functional flows
1. Switch tabs (Ongoing/Upcoming/Past) → list re-filters. 2. "Learn more" → event detail.

### States
Populated card list per tab; empty state per tab when none.

**Evidence:** evidence/map/08_view-events.png

## 9. Create Announcement / Announcements — `/fit/community/announcement`
**Access:** left nav → Engage → Community → "Create Announcement" (or global Create → Announcement).
**Purpose:** write & publish org announcements. **Layout regions:** header · info banner · "Existing
Announcements" list (toolbar + table) · (Create Announcement opens a form view).

### Elements — list page
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | heading | "Announcements" / "Write and publish announcements to your organisation." | (English — bug #15) |
| Info banner | panel | "What is an Announcement?" / "Send messages about new initiatives, events, and more to your organisation" | (English) |
| Create Announcement | button | (icon button, no accessible name) "Create Announcement" | opens create form; ⚠️ unlabeled for a11y |
| Section | label | "Existing Announcements" | |
| Search | textbox | "Search by title..." | filters list |
| View toggle | buttons | list / grid icons | switch layout |
| Table | table | column "Title" + rows | lists announcements |
| Delete announcement | button (per row) | "Delete announcement" (trash icon) | opens delete-confirmation dialog (9b) |
| Show more | button | "Show more" (+ "N remaining") | paginates/loads more |

### Form 9a — Create Announcement (form view)
Heading "Create Announcement"; **"Mit KI generieren" / AI-generate** field (prompt textarea + tone
selector "Business" + "Generate", "Minimum 30 characters"); **Title*** (placeholder "Enter the
title of the announcement"); **Description*** (placeholder "What would you like to announce today?");
**"Publish"** button. ⚠️ Bugs: header + "Publish" stay English (#15); **Publish never enables even
with both fields filled** (#29 — functional).

### Overlay 9b — Delete confirmation dialog
"Are you sure you want to delete?" / "You won't be able to revert this!" + "Cancel" / "Delete".
(Delete works; dialog is English — bug #28.)

### Functional flows
1. Create → form → fill AI-generate or Title+Description → Publish (⚠️ Publish gating bug #29).
2. Search filters list. 3. Delete (trash) → confirm dialog → removes the announcement.

**Evidence:** evidence/map/09_announcements.png (+ evidence/functional/announcement_* for form/dialog)

## 10. Publish Notifications — `/fit/community/publish-notifications`
**Access:** left nav → Engage → Communications → "Publish Notifications". **Purpose:** send targeted
in-app notifications. **Layout regions:** "Notification Content" form · "Target Audience" builder ·
"Notification Preview" (live).

### Elements
| Section | Element | Type | Exact label/text | Function / notes |
|---|---|---|---|---|
| — | Title | heading | "Publish Notification" / "Send targeted in-app notifications to employees." | |
| Notification Content | Title | textbox | "Enter notification title" + counter ("0/60") | required; counter verified |
| | Subtitle | textbox | "Enter notification subtitle (optional)" + counter ("0/150") | optional |
| Target Audience | mode tabs | buttons | "Select Users", "Attributes", "CSV Upload" | choose how to target |
| | Attribute filters | dropdowns | "Department"/"Country"/"Gender"/"Age Group" each with "is in" + "All Departments/Countries/Genders/Age Groups" | narrow audience (default = all) |
| | Load Employees | button | "Load Employees" | resolves the audience list |
| — | Send Notification | button | "Send Notification" | **⚠️ enables after only a title (default audience = everyone) — accidental-send risk, bug #27** |
| Notification Preview | live preview | "Notification Preview" / "Preview" / "Notification Title" / "Notification subtitle will appear here" / "Just now" / "Desktop View" | mirrors typed content in real time |

### Functional flows
1. Type title/subtitle → preview updates live. 2. Pick audience mode + attribute filters → Load
Employees. 3. Send Notification (🔴 sends in-app to employees — do not fire in docs).

**Evidence:** evidence/map/10_publish-notifications.png

## 11. Send Custom Email — `/fit/community/send-custom-email`
**Access:** left nav → Engage → Communications → "Send Custom Email". **Purpose:** compose & send
branded emails. **Layout regions:** "Email Content" form · "Target Audience" builder · "Email
Preview" (live).

### Elements
| Section | Element | Type | Exact label/text | Function / notes |
|---|---|---|---|---|
| — | Title | heading | "Send Custom Email" / "Compose and send branded emails to employees." | |
| — | Design a rich email | button | "Design a rich email" | opens Email Designer (screen 12) |
| Email Content | Subject Line | textbox | "Subject Line" + counter | required |
| | Headline | textbox | "Headline" + counter | required |
| | Body | textarea | "Body" + counter | |
| Target Audience | mode tabs | buttons | "Select Users", "Attributes", "CSV Upload" | targeting mode |
| | Build from Report | buttons | "Employee Report", "League Report", "Participation Report", "Wellness Score" | build audience from a report |
| | Search & add | textbox | "Build a list from reports above or search and add individual users" (+ search) | add individual users |
| — | Send Email | button | "Send Email" | 🔴 sends real emails — not fired in docs |
| Email Preview | live preview | "Email Preview" / "Email Subject" / "Inbox" / "Vantage Fit" / "Just now" | mirrors composed email |

### Functional flows
1. Compose subject/headline/body → preview updates. 2. Build audience from a report or search users.
3. "Design a rich email" → Email Designer. 4. Send Email (🔴).

**Evidence:** evidence/map/11_send-custom-email.png

## 12. Email Designer ("Rich Email Composer") — overlay
**Access:** Send Custom Email → "Design a rich email" (or nav Communications → "Email Designer").
**Purpose:** guided rich-email authoring. **Layout:** full-screen dialog with a step rail + a
start-choice screen + live preview.

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | heading | "Rich Email Composer" | (English — bug #16) |
| Close | button | "Close" | closes composer |
| Step rail | buttons | "Intro", "Write", "Design", "Send" | wizard steps |
| Value prop | heading | "People-first email" / "Send updates people actually open." + "Build a polished, on-brand email in a few guided steps - then send it from your own mailbox, so it lands like a note from a colleague." | |
| Continue last email | option | "Continue last email" / "Keep the current draft." | resume a draft |
| Start new | option | "Start new" / "Begin from a fresh template." | new email |
| Info cards | cards | "System mail gets skimmed", "Your mailbox lands harder", "Designed, not plain" (+ descriptions) | rationale |
| Get started / Import template | buttons | "Get started", "Import template" | proceed into the Write/Design steps |
| Live preview | pane | "Live preview · 600px email" with sample email layout | previews the designed email |

### Functional flows
1. Open → Intro (Continue last / Start new) → Write → Design → Send (steps).
2. Live preview reflects the composed email. (Whole composer is English — bug #16.)

**Evidence:** evidence/map/12_email-designer.png

## 13. Health Insights — `/fit/workforce-health/health-insights` — ⛔ BLOCKED
**Access:** left nav → Analyze → Workforce Health → "Health Insights". **Status:** ⛔ **BLOCKED.**
The page embeds an external dashboard (`dash-vfit.vantagecircle.org`) in an **iframe** that
**"refused to connect"** in the MCP browser (frame blocked). Its content (charts, filters, legends)
could not be observed. This is a separate embedded app, likely its own localization system.
**Needs a browser/environment where the iframe loads.** Only the surrounding chrome + the Ask
Vantage Fit launcher are present. **Evidence:** evidence/map/13_health-insights_blocked.png

## 14. Wellness Score — `/fit/workforce-health/wellness-score`
**Access:** left nav → Analyze → Workforce Health → "Wellness Score" (+ NEW badge). **Purpose:**
org wellness-score analytics. **Layout regions:** filter bar · KPI cards · Insights (AI) · a set of
breakdown charts.

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Filters | dropdowns | "All Countries", "All Departments", "All Genders", "All Age Groups", date "This Month" | scope the analytics |
| KPI cards | stat cards | "Current Score", "12-Month Average", "Industry Benchmark" (+ "vs Industry"/"below benchmark") | headline metrics |
| Insights | panel | "Insights" + "AI-generated" | AI summary |
| Chart: composition | chart | "How the Wellness Score is Composed" ("Gesamtpunktzahl"/total) | breakdown |
| Chart: trends | chart | "Component Trends Over Time" | |
| Chart: by Department | chart | "Wellness Score by Department" / "Average score ranked by performance" + legend "High (>=80)", "Moderate (70-80)", "Low (<70)" | |
| Chart: by Geography | chart | "Wellness Score by Geography" / "Regional average scores" (US, India, Austria, UK) | |
| Chart: by Age Group | chart | "Average score across age brackets" + legend (High/Moderate-High/Moderate/Low) | |
| Chart: by Gender | chart | "Average score across genders" (Male/Female) | |
| Segments | labels | "Consistent Engagers", "High Participation Users" ("Users with high weekly activity"), "Program Adherents", "Score advantage vs org average", "HR Admin Only" | segment cards |
| Employee scores | section | "Employee Wellness Scores" / "Individual employee wellness score details" | per-employee table |

### Notes (source)
Most analytics **content** (stat labels, chart titles/headers, legends, "Employee Wellness Scores")
is **backend-served** (insights API `header`/`label` fields) — see bugs #17, #21. Page **chrome**
(heading "Wellness Score", "Insights", "AI-generated", filters) is FE.

### Functional flows
1. Adjust filters/date → all charts recompute. 2. Charts have hover tooltips; sections drill into detail.

**Evidence:** evidence/map/14_wellness-score.png

## 15. Wellness Leagues — `/fit/workforce-health/wellness-leagues`
**Access:** left nav → Analyze → Workforce Health → "Wellness Leagues". **Purpose:** activity-tier
distribution & trends. **Layout regions:** filter bar · "Current Tier Distribution" · "Tier Trends
Over Time" · employee table (columns + export).

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | heading | "Wellness Leagues" / "Consistency-based activity tier distribution and trends" | |
| Filters | dropdowns | "All Countries", "All Departments", "All Genders", "All Age Groups", date "This Month" | scope data |
| Current Tier Distribution | panel | "Current Tier Distribution" + "On <date>" + "Based on avg daily steps over 21 days" | tier snapshot |
| Tier Trends Over Time | chart | "Tier Trends Over Time" + "Weekly"/"Monthly" toggle | period toggle (verified) |
| Columns picker | button | "Columns" → "Employee ID(+8 others)" | choose visible table columns (labels English — bug #20) |
| Export | button | "Export" | export the table |
| Table | table | employee rows by tier | empty-state: "No data available" / "Adjust your filters or the date range" |

### Functional flows
1. Filters recompute distribution/trends. 2. Weekly/Monthly toggles the trend period (verified).
3. Columns picker toggles table columns. 4. Export downloads.

**Evidence:** evidence/map/15_wellness-leagues.png

## 16–21. Reports (Analyze · Reports) — shared structure + per-report columns
**Access:** left nav → Analyze → Reports → (each report). All six share one layout.
**Shared structure (every report):** header (title + subtitle) · **filter bar** (audience dropdowns
"All Countries" / "All Departments" / "All Genders" / "All Age Groups" + date-range preset) ·
**column picker** (choose visible columns; button shows first column "+N others") · **Export**
button (CSV/Excel) · **data table** (report-specific columns) · **empty state** ("No data available"
/ "Adjust your filters or the date range" or "…click Generate"). Column-picker labels render English
even when headers are translated (bug #20). Some reports have an "Enrolled" status filter.

| # | Report | Route | Columns (observed this session) | Notes |
|---|---|---|---|---|
| 16 | **League Report** | `/fit/leagues` | (empty for current range) | shows "No data available" empty state; filters only |
| 17 | **Employee Report** | `/fit/employee-report` | Name · Email · Department · Country · Date of Joining · Last Active At | filters incl. "Enrolled"; column picker "Date of Joining(+5 others)" |
| 18 | **Participation Report** | `/fit/participant-report` | Name · Email · Department · Country · Date of Joining · Last Active At | same column set as Employee |
| 19 | **Incentivisation Report** | `/fit/transaction-report` | User Email · Challenge Name · Date · Reason · Country Name · Points · Value | fully headers-translated in de |
| 20 | **Wellness Score Report** | `/fit/wellness-score-report` | per-employee wellness scores ("Employee Wellness Scores" section, "HR Admin Only") | section title/subtitle are **backend-served** (bug #21) |
| 21 | **Redemption Report** | `/fit/redemption-report` | Name · Email · Department · Country · Employee ID · Company User ID · Product Name · Redeemed Points · Amount · Currency · Transaction Date | column picker "Transaction Date(+10 others)" |

### Functional flows (all reports)
1. Set audience filters + date range → (Generate where required) → table populates.
2. Column picker toggles visible columns. 3. Export downloads the report (CSV / Excel).
4. Empty state shown when no data for the selected scope.

**Evidence:** evidence/map/16_reports_league.png (empty state) + column sets captured in this
session's Run-1 testing (evidence/de/{employee,participant,incentivisation,wellness-score,redemption}-report.png).
⚠️ League Report body was empty for the current range; other reports' columns verified earlier this session.

## 22. Upload Points — `/fit/reward-hub/upload-points`
**Access:** left nav → Manage → Rewards → "Upload Points". **Purpose:** bulk-award reward points via
CSV. **Layout regions:** config form (left) · "Steps to follow" guidance (right).

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | heading | "Bulk Upload Points" / "Distribute reward points to your employees" | |
| Select Wallet | dropdown | "Select Wallet" → "Reward" | choose points wallet |
| Select Country | dropdown | "Select Country" | choose country |
| Upload Type | radio group | "Primary", "Appreciations", "Points CSV with Custom Email Template" | template type |
| Download Sample | button | "Download Sample" / "Download sample CSV" | downloads CSV template for selected type |
| File dropzone | upload | "Click to upload or drag and drop" | upload the filled CSV |
| Send email toggle | switch | "Send Email to recipients" | (from prior) email recipients |
| Steps to follow | guidance | "Steps to follow" + numbered steps (select wallet/country/type → download template → edit → upload) | instructions |
| Preview / Submit | buttons | "Preview", "Submit" | disabled until wallet+country+type+file set (validation-gated); Submit 🔴 distributes real points |

### Functional flows
1. Select wallet + country + upload type → download sample → fill CSV → upload → Preview → Submit.
2. Validation gates Preview/Submit until required fields + file present.

**Evidence:** evidence/map/22_upload-points.png

## 23. Add Employees — `/fit/configuration/add-employees`
**Access:** left nav → Manage → Configuration → "Add Employees". **Purpose:** bulk add/update
employees via CSV. **Layout regions:** upload form + steps · "Note" (CSV field rules).

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | text | "Add Employees" / "Upload the list of your employee's details and we'll manage the setup." | |
| Steps | guidance | "Start by downloading our ready-to-use template", "Edit the template with the details…", "Upload the completed file…" | |
| Download template | link/button | (download ready-to-use template) | gets the CSV template |
| File dropzone | upload | "Click to upload or drag and drop" | upload filled CSV |
| Note | panel | "Note" + rules: "company_id" (unique company ID); "status" ("Status = 1 for add/update actions.", "Status = 0 for all other actions (delete, deactivate, exit, terminate).", "Any value other than 1 will be treated as a Delete/Deactivate/Exit/Terminate case.") | field-format help |
| View More | button | "View More" | expands the note |
| Cancel / Preview / Submit | buttons | "Cancel", "Preview", "Submit" | Preview/Submit disabled until a file is uploaded (validation-gated); Submit 🔴 adds/updates real employees |

### Functional flows
1. Download template → fill → upload → Preview → Submit. 2. "View More" expands rules. 3. Cancel clears.

**Evidence:** evidence/map/23_add-employees.png

## 24. Preview Emails — `/fit/configuration/preview-emails`
**Access:** left nav → Manage → Configuration → "Preview Emails". **Purpose:** preview & enable/disable
the system emails employees receive. **Layout:** list of email-type cards.

### Elements
| Element | Type | Exact label/text | Function / notes |
|---|---|---|---|
| Title | heading | "Preview Emails" / "Manage email notifications sent to employees. 9 of 9 enabled" | |
| Email-type card ×9 | card | "Welcome Email (Add Employee)", "Welcome Email (Invite to Challenge)", "Intro to App", "Challenge Reminder", "Challenge Start", "Weekly Summary", "Challenge Completion", "Event Invite / RSVP Confirmation", "Direct Message from HR" — each + a description | titles/descriptions **backend-served** (bug #23) |
| Open in New Tab | link (per card) | "Open in New Tab" | opens the rendered email preview in a new tab |
| Enable toggle | switch (per card) | on/off | enable/disable that email; some may be locked (system-managed) |
| Footer | note | "About Email Settings" (explains locked/system emails, preview behaviour) | |

### Functional flows
1. Toggle a card to enable/disable that notification. 2. "Open in New Tab" renders the email preview.

**Evidence:** evidence/map/24_preview-emails.png

## 25. Settings — `/fit/configuration/settings`
**Access:** left nav → Manage → Configuration → "Settings". **Purpose:** configure email/challenge/
app behaviour. **Layout regions:** three grouped setting sections. **Note:** no separate UI-language
setting here (the left-rail switcher is the only language control). 🔴 toggles are org-wide (may
auto-save) — not flipped during documentation.

### Elements
| Section | Element | Type | Exact label/text | Function / notes |
|---|---|---|---|---|
| — | Title | heading | "Settings" | |
| Email Settings | Section | heading | "Email Settings" / "Email banner and notification preferences" | |
| | Banner | image config | "Recommended banner size" / "PNG only" + Change/"Remove" | email banner upload |
| | Challenge completion email | switch | "Challenge completion email" / "Send email when a user completes a challenge" | toggle |
| | Disable all emails | switch | "Disable all emails" / "Disable all Vantage Fit marketing/system emails" | master toggle |
| Challenge Settings | Section | heading | "Challenge Settings" / "Configure team and challenge behavior" | |
| | Users can create teams | switch | "Users can create teams" / "Allow users to create their own teams within challenges" | |
| | Users can update teams | switch | "Users can update teams" / "Allow users to modify team details (name, photo)" | |
| | Max team size | spinbutton | "Max team size" / "Maximum number of members allowed per team" (placeholder "Enter") | numeric |
| | Team breakdown | switch | "Show team breakdown in leaderboard" / "Show team-level breakdown in the challenge leaderboard" | |
| App Settings | Section | heading | "App Settings" / "In-app logo and app-wide configurations" | |
| | Logo | image config | "Recommended logo size" / "PNG only" + Change/"Remove" | in-app logo upload |
| | Multiple activity save check | switch | "Multiple activity save check" / "Prevent users from saving overlapping or duplicate workouts and activities" | |

### Functional flows
1. Toggle switches / set values → settings persist org-wide (🔴 auto-save risk). 2. Upload/Remove banner & logo.

**Evidence:** evidence/map/25_settings.png

## Completion & verification (Phase 1)

**All 26 tracker rows resolved:** 24 ✅ fully documented · 1 ◐ partial (**#2 Create Challenge** —
landing + builder steps 1–2 done; deeper wizard steps flagged for a dedicated pass) · 1 ⛔ blocked
(**#13 Health Insights** — external iframe refused to connect).

**Grounding:** every screen entry was built from a live accessibility snapshot + screenshot
(evidence in `evidence/map/`), labels quoted from the snapshot; interactive overlays (filters, date
picker, Ask-Vantage-Fit modal, create-choosers, wizards, dialogs, column pickers) opened and
documented where present. No destructive writes were performed.

**Known follow-ups for full exhaustiveness:**
- #2: walk the Custom Challenge wizard beyond "Set Duration" (activities/targets → audience →
  rewards → publish) and the other four challenge-type builders.
- #13: re-run Health Insights in an environment where the `dash-vfit.vantagecircle.org` iframe loads.
- Overview KPI cards intermittently skeleton (observation O1) — chart labels captured when rendered.

**Sets up the next phases (per user):**
- **Phase 2** — from this element inventory, tag every string as frontend (in `en.json`/`de.json` or
  a JS-bundle literal) vs backend (API-served). The "Function / on-interact" column also lists
  every behaviour to test.
- **Phase 3** — per module, test each frontend string **and each functional flow** across languages
  (incl. behaviour bugs like a filter that doesn't respond when clicked in a non-English locale).






















