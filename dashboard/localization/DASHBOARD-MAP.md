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
| 5 | Engage · Programs | Content Library | `/fit/programs/on-demand-content` | ☐ |
| 6 | Engage · Programs | Create Content (chooser + forms/builders) | `…?action=create` / `/fit/create-bite-size-content` | ☐ |
| 7 | Engage · Community | Create Event | `/fit/events/create-event` | ☐ |
| 8 | Engage · Community | View Events | `/fit/events` | ☐ |
| 9 | Engage · Community | Create Announcement | `/fit/community/announcement` | ☐ |
| 10 | Engage · Communications | Publish Notifications | `/fit/community/publish-notifications` | ☐ |
| 11 | Engage · Communications | Send Custom Email | `/fit/community/send-custom-email` | ☐ |
| 12 | Engage · Communications | Email Designer | (overlay) | ☐ |
| 13 | Analyze · Workforce Health | Health Insights | `/fit/workforce-health/health-insights` | ☐ |
| 14 | Analyze · Workforce Health | Wellness Score | `/fit/workforce-health/wellness-score` | ☐ |
| 15 | Analyze · Workforce Health | Wellness Leagues | `/fit/workforce-health/wellness-leagues` | ☐ |
| 16 | Analyze · Reports | League Report | `/fit/leagues` | ☐ |
| 17 | Analyze · Reports | Employee Report | `/fit/employee-report` | ☐ |
| 18 | Analyze · Reports | Participation Report | `/fit/participant-report` | ☐ |
| 19 | Analyze · Reports | Incentivisation Report | `/fit/transaction-report` | ☐ |
| 20 | Analyze · Reports | Wellness Score Report | `/fit/wellness-score-report` | ☐ |
| 21 | Analyze · Reports | Redemption Report | `/fit/redemption-report` | ☐ |
| 22 | Manage · Rewards | Upload Points | `/fit/reward-hub/upload-points` | ☐ |
| 23 | Manage · Configuration | Add Employees | `/fit/configuration/add-employees` | ☐ |
| 24 | Manage · Configuration | Preview Emails | `/fit/configuration/preview-emails` | ☐ |
| 25 | Manage · Configuration | Settings | `/fit/configuration/settings` | ☐ |

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

<!-- Screen entries are appended below in tracker order. -->





