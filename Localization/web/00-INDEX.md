# Employee Fit Web — Localization Testing INDEX

**Entry point for the employee-facing Vantage Fit web engagement.** Start here.

**Surface:** `app.vantagecircle.co.in/ng/fit/*` (heart icon) · **Tenant:** UAT · **Bug IDs:** sequential `B1`…`B28`
**Skill:** `localization-testing` · **Reporting:** `localization-bug-reporting`
**Compiled:** 2026-07-29

> ⚠️ This is the **employee** surface. The **admin dashboard** is a separate engagement with its own IDs
> (`OV#1`, `CC#2`, `RPT#4`…) under `Localization/dashboard/`. Do not mix findings.

## Which file is authoritative

| File | Role |
|---|---|
| **`bugs/bug-log.md`** | **SOURCE OF RECORD.** All 28 bugs in full. New findings append here first |
| `bugs/<module>.md` | Per-module bug detail (5 files) |
| `test-cases/<module>.md` | Executed cases with Actual/Status filled (5 files) |
| **`COVERAGE_ANALYSIS.md`** | **Read before claiming completeness** — W1–W19 gap register |
| `Localization_Checklist.md` | Per-screen + cross-module consistency checklist |
| `Coverage_Matrix.md` · `Execution_Status.md` | Module × language matrix · dated run history |
| `<Language>_Pass_Conclusion.md` | One per language pass (de, es, fr, pt) |

**Status:** 28 bugs logged · **0 categorised into a report · 0 filed to Jira.** See `COVERAGE_ANALYSIS.md` W19.

---

# Part 1 — Module inventory: every submodule, surface and flow

**Legend:** ✅ verified · ◐ partial · ⚠️ recorded but unreliable · ❌ never tested · ⬜ exists, not reached ·
❓ existence unconfirmed

Everything below is **derived from the executed test cases** — no invented surfaces. Items marked ❓ are
things the docs imply but never confirmed; discover them before assuming.

---

## 1. Global chrome (present on every route — test once per language, then spot-check per route)

| # | Surface | Elements | Status |
|---|---|---|---|
| 1.1 | Top nav | 4 tabs: Summary · Challenges · Programs · Community | ✅ (❌ de "Challenges" = B3) |
| 1.2 | `+ Add` button | Label + click behaviour | ◐ label ✅ · **behaviour ⬜ — didn't open on the one attempt (B-none, inconclusive)** |
| 1.3 | Footer | "Scan to sign in on your phone" · motivational tagline ("Sweat now, Shine later.") · "© 2026 Vantage Fit…" · "Need Help with Vantage Fit?" | ✅ (❌ English on Community route = B16) |
| 1.4 | Bottom mini-nav | Home / Work | ✅ (resolves locale independently — diagnostic, see B16) |
| 1.5 | `<html lang>` | Matches selected locale | ✅ **correct per locale** (better than the dashboard) |
| 1.6 | Loading / skeleton states | Any spinner or skeleton copy | ❌ **never captured** |
| 1.7 | Global error / offline state | Network-failure copy | ❌ never (one transient 502 seen — B24) |
| 1.8 | **Promotional interstitial modal** (NEW, found 2026-07-29) | "Make memories, not just plans!" / "Redeem Now" · `button.vc-modal-close-btn` · blurs the page behind | ◐ dismiss ✅ · **localization never checked** |

## 2. Profile & language (the switch surface — a test target, not just plumbing)

| # | Surface / flow | Elements | CRUD | Status |
|---|---|---|---|---|
| 2.1 | Profile avatar → View Profile | Menu labels | R | ◐ traversed, not string-audited |
| 2.2 | My Profile → **My Info** | Field labels, values | R | ◐ |
| 2.3 | **Edit Profile → Language → Save** | Dropdown **option names**, Save/Cancel | **U** | ◐ flow works; **are the language names themselves localized?** ❓ (the dashboard fails this — SET#1) |
| 2.4 | Language-change confirmation alert | Native browser alert text | — | ✅ → **FAILS: B2** (`{language}` literal in de/fr/es/pt; only en interpolates) |
| 2.5 | Forced logout → re-login | Auth screens at `api.vantagecircle.co.in` | — | ❌ **login/auth screens never localization-tested** |
| 2.6 | Language persistence after re-login | Does the choice survive? | — | ✅ → **FAILS: B11** (reverts to English) |
| 2.7 | Other profile fields (name, dept, city, country) | Labels + edit flows | U | ❌ never |

## 3. SUMMARY — `/ng/fit/summary`

| # | Surface | Elements | Status |
|---|---|---|---|
| 3.1 | Header date line | "Friday, 24 July 2026" | ✅ → **FAILS: B1** (English in all 4 langs) |
| 3.2 | **Snapshot** card | Steps `5000/10000` `50%` · Active Minutes `983/32 mins` `100%` · **"Open Diary"** CTA | ✅ (❌ units = B6) |
| 3.3 | **Trends** card | Avg Steps `/day` · Active Minutes `hrs mins` · Mindful Minutes · Avg Sleep · weekday axis `S M T W T F` · ranges "18 - 24 Jul" · **"View Trends"** CTA | ✅ (❌ B1, B6, B7) |
| 3.4 | **Challenges** card | Week badge · Weekly Rank · challenge name (BE) · Weekly progress % | ✅ (❌ "Week 1" = B4) |
| 3.5 | **Your latest badge** card | Heading + date | ✅ (❌ B1 date; ❌ formal register = B12) |
| 3.6 | **Vitals** card | Hemoglobin · `g/dL` · "Updated on …" | ✅ (❌ B1) |
| 3.7 | **Health** card | Wellness Score value · "Updated on …" | ✅ (⚠️ "Wellness Score" EN = B9, brand judgment) |
| 3.8 | **Highlights** card | "See what your community is up to" · **"View all"** · post title (BE) · relative time · "Posted by" · "N Likes \| N Comments" | ✅ → **FAILS: B5** (social strings), B1 |
| 3.9 | Flow: Snapshot → **Diary** | Navigation | ✅ works |
| 3.10 | Flow: Trends card → **Trends** | Navigation | ✅ works |
| 3.11 | Flow: Highlights "View all" | Destination + its strings | ⬜ **not reached** |
| 3.12 | Empty states (no challenge / no badge / no vitals) | Copy | ❌ never — account always had data |

## 4. CHALLENGES — `/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing`

### 4a. Listing sub-tabs

| # | Surface | Status |
|---|---|---|
| 4.1 | Subtitle "Compete with colleagues and track your tasks." | ✅ |
| 4.2 | **Ongoing** sub-tab | ✅ |
| 4.3 | **Upcoming** sub-tab | ✅ **functional re-verified 2026-07-29 (en)**: URL `?tab=upcoming`, tab marked active, content swaps. ⚠️ uses a **different card template** — untracked. Reward strings carry formatted numbers ("Earn 10,000 Fit Points") = untested locale target |
| 4.4 | **Past** sub-tab | ✅ **functional re-verified 2026-07-29 (en)**: 0 overflow breaks, third card template (title + date range). Label confirmed `Past` ("Completed" was wrong) → **NEW B32**: one row shows end date before start |
| 4.5 | Sub-tab labels localized | ◐ CHL-LOC-008 = **Needs Verification** |

### 4b. Card types (4 distinct templates — each needs its own check)

| # | Card | Elements | Status |
|---|---|---|---|
| 4.6 | Weekly challenge card | Week badge · Weekly Rank · Weekly progress % | ✅ (❌ B4) |
| 4.7 | Journey / milestone card | "Next milestone: Chichen Itza / Machu Pichu" · Overall Rank · Milestone progress % | ✅ |
| 4.8 | E-Marathon card | "E-Marathon Challenge (ends in 27 days)" · Overall progress % | ✅ |
| 4.9 | Race card | "Race Challenge (ends in 7 days)" | ✅ |

### 4c. Challenge detail page

| # | Surface | Elements | Status |
|---|---|---|---|
| 4.10 | Breadcrumb + "Back" | | ◐ English during B25 state |
| 4.11 | Status sentence | "Este desafío tiene Finalizado" — template + capitalized status word | ◐ **copy defect noted, P4, not separately logged** |
| 4.12 | **Weekly task list — reward lines** | "Gane 500 puntos" | ✅ (BE-sourced, correctly localized) |
| 4.13 | **Weekly task list — task instructions** | 3 task sentences | ✅ → **FAILS: B27** (untranslated `fl oz`, "fl oz vasos", "1 días") + **B12** (formal imperatives) |
| 4.14 | **Leaderboard** section | "Leaderboard" heading · "You" row label | ◐ both English — folded into the general pattern, **not separately logged** |
| 4.15 | Adherence-activity answer options | `["No","Yes"]` | ✅ → **FAILS: B26** (BE, "Yes" should be "Sí") |
| 4.16 | Task completion / check-in flow | **C/U** | ❌ **never tested** — no task was ever completed |
| 4.17 | Join / leave a challenge | **C / D** | ❌ never (blast-radius) |
| 4.18 | `+ Add` → create challenge | **C** | ❌ **never** — button toggled, no menu appeared |

## 5. PROGRAMS — `/ng/fit/programs`

### 5a. Library sub-tab

| # | Surface | Status |
|---|---|---|
| 5.1 | Subtitle "Library content and wellness offerings." | ✅ |
| 5.2 | Sub-tab labels Library / Offerings | ✅ |
| 5.3 | **Featured Content** → "Curated Health Content for you" | ◐ **Needs Verification** — section absent for de (no featured items); needs a locale that has them |
| 5.4 | **Health bites** → "15-30 sec tips" | ✅ |
| 5.5 | Category carousels: Excercise · Healthy Eating · Mindfuless | ◐ **EN source typos** ("Excercise", "Mindfuless") — owner unconfirmed, P4 |
| 5.6 | Content cards (titles) | ✅ BE/authored — correctly untranslated. ⚠️ es has placeholder titles ("Spanish Content") = content-quality note |
| 5.7 | Content **thumbnails** | ✅ → **FAILS: B23** — 28 URLs 404, render as solid black boxes |
| 5.8 | Motivational background tagline | ✅ |
| 5.9 | **"View all"** → modal grid | ✅ → **FAILS: B14** (empty grid, de-specific; pt data point discarded as confounded) |
| 5.10 | Library content set matches account language | ✅ → **FAILS: B25** (served the EN baseline set mid-session) |

### 5b. Offerings sub-tab

| # | Surface | Status |
|---|---|---|
| 5.11 | **Category** filter (Physical / Mental) | ✅ |
| 5.12 | **Subcategory** filter (Workout / Dance / Meditation) | ✅ |
| 5.13 | Filter selection behaviour | ✅ functional |
| 5.14 | Filter **empty state** | ✅ → **FAILS** "No offerings found in this category." (English; env-state-dependent) |
| 5.15 | "Partner offers" heading + subtitle | ✅ (❌ formal register = B12) |
| 5.16 | Partner offer cards | ✅ BE/authored, correctly untranslated |
| 5.17 | Flow: partner card → **external partner site** in new tab | ✅ documented — no in-app detail page; out of scope |
| 5.18 | Offerings load reliability | ✅ → **FAILS: B24** (intermittent 502 → "Unable to load offerings right now" + Try again) |

### 5c. Bite-size content detail (a multi-step reader — the closest thing to a wizard on this surface)

| # | Surface | Status |
|---|---|---|
| 5.19 | Step 1: "Introduction" heading + body | ✅ |
| 5.20 | **"Written By"** label + author name | ✅ → **FAILS: B13** (label EN; author name correctly kept) |
| 5.21 | Body register | ✅ → **FAILS: B12** (formal, inside authored content) |
| 5.22 | Step 1 CTA ("Let's start") **placement** | ✅ → **FAILS: B15** (button splits the paragraph; language-independent) |
| 5.23 | Step 1 → Step 2 navigation | ✅ works, step 2 fully localized |
| 5.24 | Steps 3+ / completion / end state | ⬜ **never reached** — only step 2 verified |
| 5.25 | "About this challenge" info popover | ⬜ never opened |

## 6. COMMUNITY — `/ng/fit/community`

### 6a. Social sub-tab

| # | Surface | Status |
|---|---|---|
| 6.1 | Heading "Community" + subtitle | ✅ → **FAILS: B16** (English, both langs) |
| 6.2 | Sub-tab labels Social / Events | ✅ → **FAILS: B16** |
| 6.3 | Left-rail profile card (name / dept / city) | ◐ traversed, labels not audited |
| 6.4 | **FROM LEADERSHIP** → "A note from CEO" + role label | ✅ → **FAILS: B16** |
| 6.5 | Flow: CEO card click | ✅ documented — opens the **raw CDN video URL** in a new tab, no in-app player |
| 6.6 | Post feed — **empty state** | ✅ correctly localized (the "reverse signal" — shared component) |
| 6.7 | Post feed — **populated** post cards | ❌ **never** — no posts existed. Re-test when posts exist |
| 6.8 | Right-rail challenge widget | ✅ (❌ B4) |
| 6.9 | Right-rail badge widget | ✅ (❌ B12 formal register) |
| 6.10 | Create post / add-post flow | **C** | ❌ never |
| 6.11 | Like / comment on a post | **C/U** | ❌ never |

### 6b. Events sub-tab

| # | Surface | Status |
|---|---|---|
| 6.12 | "Event Calendar" header | ✅ → **FAILS: B16** |
| 6.13 | Week-strip weekday abbreviations (MON TUE WED…) | ✅ → **FAILS: B16** |
| 6.14 | Week-strip dates | ◐ |
| 6.15 | "Upcoming events" + empty state | ✅ → **FAILS: B16** |
| 6.16 | Populated event cards | ❌ never — no events existed |
| 6.17 | Event detail / RSVP / join flow | **C/U** | ❌ never |
| 6.18 | Create event flow | **C** | ❌ never |

## 7. DIARY — `/ng/fit/summary/diary` (reached via Summary's Snapshot card)

| # | Surface | Elements | CRUD | Status |
|---|---|---|---|---|
| 7.1 | Heading + date | "Today · 28 July 2026" | R | ✅ (❌ B1) |
| 7.2 | **Date-stepper** | Previous day / Today / Next day | R | ✅ labels + **functional ✅** |
| 7.3 | Snapshot card | Steps / Active Minutes | R | ✅ |
| 7.4 | **Calorie Ledger** card (EN label confirmed 2026-07-29; earlier docs said "Calorie Balance") | "Recommended" kcal · Meals / Resting / Active / Balance breakdown · **deficit/surplus sentence** · "Learn more" link | R | ✅ → **FAILS: B17** (status sentence EN inside an otherwise-German card) |
| 7.5 | Flow: "Learn more" | Destination | R | ⬜ never clicked |
| 7.6 | **Nutrition / Food Log** | Section + empty state | R | ✅ empty state localized |
| 7.7 | Nutrition Log — **add a meal** | | **C** | ⭕ **N/A if app-only** — verify the label, then close as by-design |
| 7.8 | **Sleep** | Section + "No Data" + prompt | R | ✅ |
| 7.9 | Sleep — log sleep | | **C** | ⭕ **N/A — app-only** (user-confirmed 2026-07-29: trackables labelled "track on app" are not web-loggable) |
| 7.10 | **Intake** | Calories / Water values | R | ✅ (water stays metric — expected) |
| 7.11 | **"Log Water"** modal — open | Modal chrome + **dialog semantics** | R | ✅ opens → **FAILS a11y: B30** (no `role`/`aria-modal`/name; focus not moved into the dialog) |
| 7.12 | Log Water — **unit toggle ml ⇄ fl oz** | Goal value · slider scale · **"1 glass = 250 ml" helper** | U | ✅ → **FAILS: B28**, now confirmed **in ENGLISH too** → language-independent unit-conversion bug, not a translation defect |
| 7.13 | Log Water — **"Any amount" slider** | | U | ✅ converts |
| 7.14 | Log Water — **submit** | Value update + success feedback | **C** | ⚠️ → **NEW B31**: submitting with no amount **closes the dialog with zero feedback**. Toast absence **CONFIRMED** (observer + 2.5 s wait). Submit-with-amount still to re-verify |
| 7.15 | **Distance** | Covered / Jog-Run / Cycling + unit | R | ✅ → **FAILS: B18** ("mile" unit word EN) |
| 7.16 | **Activities** | Section + empty state | R | ✅ |
| 7.17 | Activities — add an activity | | **C** | ⭕ **N/A — app-only** (user-confirmed) |
| 7.18 | **Vitals** | Mood / Heart Rate / Weight + edit buttons + **aria-labels** | R | ✅ (aria-labels ✅ localized; ❓ mood **value** "Not Good" EN — Needs Verification, likely BE) |
| 7.19 | **Mood edit** modal (`aria-label="Log mood"`) | "How are you feeling?" · 5-point scale · reason chips · "Update" | **C/U** | ◐ opens ✅, functional ✅. **Check B30's dialog-semantics defect here too** |
| 7.20 | **Heart-rate edit** flow | | U | ⭕ **N/A — app-only** ("Edit heart rate on the app") |
| 7.21 | **Weight edit** flow (`aria-label="Log weight"`) | | U | ❌ **never opened — and it IS web-available** (confirmed in markup), so a genuine gap, not app-only |
| 7.22 | "Edit heart rate on the app" | app-only affordance | — | ✅ documented as not web-reachable by design |
| 7.23 | Back navigation | → Summary (`?navBack=true`) | — | ✅ |
| 7.24 | Historical dates with richer data | | R | ❌ never — only today + one empty prior day |

## 8. TRENDS — `/ng/fit/activity-stats` (reached via Diary's Snapshot card)

| # | Surface | Status |
|---|---|---|
| 8.1 | **Metric switcher** (Steps / Active Minutes) | ✅ localized |
| 8.2 | Metric switcher — **selection pill width** | ✅ → **FAILS: B22** (pill 144px vs segment 103.75px → 40px overflow, hides neighbour's first letter) |
| 8.3 | **Week / Month / Year** range tabs | ✅ → **FAILS: B19** (all 3 English in every state) |
| 8.4 | Week-view axis (weekday + date) | ✅ → **FAILS: B7** |
| 8.5 | Month-view axis ("Week N") | ✅ → **FAILS: B4** |
| 8.6 | Year-view axis (month abbreviations) | ✅ → **FAILS: B19** (English beside a correct "Dieser Monat" — proves inconsistent wire-up) |
| 8.7 | Chart title ("Steps Overview") | ✅ → **FAILS: B19** |
| 8.8 | "Activity Details" section + date + value label | ✅ → **FAILS: B19**, B1 |
| 8.9 | Value units ("20 hrs 18 mins") | ✅ → **FAILS: B6** |
| 8.10 | Chart interaction (hover / tap a bar) | ⬜ never |
| 8.11 | Nav/footer behaviour on this route | ✅ **language-dependent** — correct in de, regresses to English in es (B19/B20) |

---

# Part 2 — CRUD reality check

The employee surface is **read-heavy**. Unlike the admin dashboard there is little true CRUD, so don't plan
for a dashboard-style CRUD sweep — but what exists is barely covered:

| Operation | Surface | Status |
|---|---|---|
| **C** — Log water | Diary → Intake | ◐ submit works, **toast unverified** |
| **C/U** — Mood entry | Diary → Vitals | ◐ opened + functional, chrome English at test time |
| **U** — Heart rate | Diary → Vitals | ❌ never |
| **U** — Weight | Diary → Vitals | ❌ never |
| **C** — Add meal | Diary → Nutrition Log | ❌ never |
| **C** — Add activity | Diary → Activities | ❌ never |
| **U** — Profile language | Profile → Edit Profile | ✅ (the switch mechanism itself) |
| **U** — Other profile fields | Profile → My Info | ❌ never |
| **C** — Create challenge | Challenges → `+ Add` | ❌ never — entry point didn't open |
| **C/U** — Complete a challenge task | Challenge detail | ❌ never |
| **C/D** — Join / leave a challenge | Challenges | ❌ never |
| **C** — Create post | Community → Social | ❌ never |
| **C/U** — Like / comment | Community → Social | ❌ never |
| **C** — Create event / RSVP | Community → Events | ❌ never |
| **D** — any delete | — | ❓ **no delete operation identified on this surface** — confirm whether any exists |

**Scope clarification (user-confirmed 2026-07-29):** trackables whose UI says **"track on app"** are **not
web-loggable by design** — sleep, activities, heart rate and (pending label check) meals. These are **⭕ N/A**,
not gaps. That leaves a much smaller real write surface.

**Net: of the genuinely web-available write operations, 2 have been exercised, both only partially.**

---

# Part 3 — The checklist to apply to every surface above

Per-screen, per-language. Mark ✅ / ❌ (→ bug id) / ⚠️ judgment / N/A. Full version:
`Localization_Checklist.md`. Dimension IDs match the admin dashboard's so coverage is comparable.

## UI / UX
- **U1** Strings translated
- **U2** No raw keys, unresolved placeholders (`{language}`), or broken concatenation
- **U3** Correct language, no cross-language bleed
- **U4** Layout intact — truncation / overflow / overlap. **Measure `scrollWidth > clientWidth`; classify CLIP / SPILL / SCROLL(not a defect); run at 4 widths with English controls**
- **U5** RTL correct (Arabic) — mirroring, `dir`, icon flip
- **U6** Glyphs / encoding — no tofu or mojibake
- **U7** Locale formatting — dates (format **and** month/weekday names), relative time, units (mins/sec/hrs/`/day`/g/dL/mile/fl oz), numbers, %, currency, chart axis labels
- **U8** States localized — empty / loading / **error** / success / no-results
- **U9** Terminology + tone — one concept → one term; one register (**default informal** `du`/`tu`/`tú`/`você`)
- **U10** Accessibility — `<html lang>`, aria-labels on icon buttons, focus order, contrast, touch targets

## Functional
- **F1** Control responds · **F2** Sub-behaviour correct (filter filters, tab switches, stepper steps)
- **F3** Validation gating + localized validation messages
- **F4** CRUD + **toasts** — install a MutationObserver *before* the action, **wait ~2 s**, then read
- **F5** Dialogs / modals localized
- **F6** Accented input in search
- **F7** Multi-step flows stay localized between steps
- **F8** Language switch applies · **persists across re-login** · no missing-key console errors
- **F9** Wire-up — where a translation exists, the component renders it

## API / source
- **A1** Locale propagation (`accept-language` sent)
- **A2** String source confirmed (English baseline / API body / bundle)
- **A3** i18n files load + key parity — ⛔ **BLOCKED by B10** on this surface
- **A4** Formatting source (client vs server pre-formatted)
- **A5** Backend strings identified and marked expected-English

## Per-language consistency pass (once per language, on the captured dumps)
Tone/register · Word/terminology (glossary) · Context/coherence. Use **Unicode-aware** word boundaries —
JS `\b` is ASCII-only and misfires on accented text. **Exclude authored content** before flagging dates.

---

# Part 4 — Coverage summary against this inventory

| | Count |
|---|---|
| Surfaces / flows enumerated above | **~95** |
| ✅ verified in ≥1 language | ~52 |
| ◐ partial or state-degraded | ~16 |
| ⬜ exists, never reached | ~8 |
| ❌ never tested | ~17 |
| ❓ existence or ownership unconfirmed | ~5 |

**Never touched at all:** all login/auth screens · loading states · populated post & event cards · 13 of 15
write operations · heart-rate and weight edits · challenge task completion · chart interaction · bite-size
steps 3+ · Highlights "View all" destination · historical Diary dates.

**Structural gaps that apply to every row above** (details in `COVERAGE_ANALYSIS.md`): no viewport-width
testing (W1), no overflow detector (W2), no Arabic/RTL (W3), dictionary parity blocked (W4), 3 of 5 modules
lack an English baseline (W7), `accept-language` never verified (W8), 12 of 16 profile languages untested
(W12), timezone never tested (W16), no non-India server (W17).

**Read this index as scope, not as achievement** — the ✅ marks are point-in-time (bug **B25**) and cover
2 solid languages, not 4.
