# SCOPE — Vantage Fit Admin Localization (VF-2207)

**URL:** `https://dashboard-v2.vantagecircle.co.in/fit/overview` (India instance)
**Access:** employee app → profile menu → **HR Admin Dashboard** (token-handshake, opens new tab) → navigate to `/fit/*`. Direct deep-link to `/fit/overview` alone bounces to the employee app.
**Logged in as:** Meghna (admin). **Plan:** Grow.
**STEP 0 status:** module map built from left-nav + switcher enumerated. **Per-screen states filled in as each screen is visited.** English baseline: `evidence/en/overview_landing.png`.

---

## ⚠️ Decisions needed before testing (STOP — confirm with QA)

### 1. The switcher is labeled **"Content language"**, and it offers **18 languages**
The only language control found is a dropdown in the left rail labeled **"Content language"** (not "Display language" / "UI language"). This raises the core AC1 question:
- Does switching **"Content language"** re-render the **externalised admin UI strings** (nav, buttons, headings, labels) — i.e. what VF-2206 translated — **or only dynamic content** (challenge names, program content)?
- If it only changes content and the UI chrome stays English, either there is a **separate UI-language switcher I haven't found** (I'll re-check the profile menu + Configuration → Settings), or the UI is not wired to it (**AC1 finding**).
- **This is the very first thing I'll verify after you confirm scope** — flip the switcher on the Overview page and watch whether the nav/labels change.

### 2. 18 languages is a large matrix — pick a risk-based subset
Full option list: **English (default), Arabic, Chinese (Simplified), Dutch, French, French (Canada), German, Italian, Korean, Portuguese, Russian, Spanish, Vietnamese, Indonesian, Polish, Hungarian, Hindi, Odia.**
Testing all 18 × ~24 screens × 6 passes is very large. Recommended risk-based subset (max coverage of failure modes per the TEST-PLAN):
- **German** — longest strings + compound words → #1 truncation/overflow risk
- **Arabic** — **RTL** — the highest-risk layout axis (mirroring, alignment, `dir`); flag if RTL isn't handled
- **French** — spacing-before-punctuation rules, accents; **French (Canada)** only if formatting diverges
- **Russian / Chinese (Simplified) / Korean** — non-Latin & CJK glyph rendering (Cyrillic, Hanzi, Hangul)
- **Hindi / Odia** — Indic scripts (complex shaping; Odia especially under-tested)
- **Spanish** — inverted `¿ ¡`, common enterprise baseline
**My recommendation:** German + Arabic + French + Chinese (Simplified) + Hindi as the Phase-1 deep set (covers length, RTL, punctuation, CJK, Indic).

**DECISION (2026-07-10):** Start with **German only** — all screens, all 6 passes — then review findings before expanding to more languages. Rationale: the "Content language" switcher behavior is still unverified, so prove the framework + process on the highest-risk language first.

### 3. Evidence folders
Currently only `evidence/en/` exists. I'll create one subfolder per **confirmed** language (locale code) once you pick the set.

---

## Out of scope (other products in the top icon rail — NOT Vantage Fit)
Recognition · Pulse · Redemption · Milestone · Perks · Admin Hub. Also backend-sourced/dynamic content (challenge names, program content, user data) per Phase-1 frontend-only rule.

---

## Module map — Vantage Fit admin (left-nav)

Legend for language cells (filled during testing): ✅ pass · ⚠️ issue logged · ❌ fail · ⭕ BE-deferred · — not reached

| # | Group | Screen | Route | States to check | EN | (langs TBD) | Notes |
|---|---|---|---|---|---|---|---|
| 1 | — | **Overview** | `/fit/overview` | default, country filter, date-range picker, "Ask Vantage Fit" (⌘K) modal | ✅ baseline | | 20+ console `disableRange` TypeErrors (date picker) — pre-existing, non-i18n; noted |
| 2 | Challenges | **Create Challenge** | `/fit/create-challenge` | multi-step form, field labels, validation, dropdowns | | | CRUD-create; use `QA-LOC-` data |
| 3 | Challenges | **Active Challenges** | `/fit/manage-challenge` | list (72), row actions, filters, pagination, empty | | | |
| 4 | Challenges | **Past Challenges** | `/fit/past-challenges` | list, empty state | | | |
| 5 | Engage · Programs | **Create Content** | `/fit/programs/on-demand-content?action=create` | builder form, validation | | | Related to VF-2126 content builder |
| 6 | Engage · Programs | **Content Library** | `/fit/programs/on-demand-content` | list, "FREE" badge, filters | | | |
| 7 | Engage · Community | **Create Event** | `/fit/events/create-event` | form, date/time, validation | | | |
| 8 | Engage · Community | **View Events** | `/fit/events` | list, empty | | | |
| 9 | Engage · Community | **Create Announcement** | `/fit/community/announcement` | form | | | |
| 10 | Engage · Communications | **Publish Notifications** | `/fit/community/publish-notifications` | form, templates | | | |
| 11 | Engage · Communications | **Send Custom Email** | `/fit/community/send-custom-email` | form, editor | | | |
| 12 | Engage · Communications | **Email Designer** | (opens designer) | editor UI | | | Route not exposed in nav; verify entry |
| 13 | Analyze · Workforce Health | **Health Insights** | `/fit/workforce-health/health-insights` | charts, filters, legends | | | Chart axis/legend localization |
| 14 | Analyze · Workforce Health | **Wellness Score** | `/fit/workforce-health/wellness-score` | "NEW" badge, charts | | | |
| 15 | Analyze · Workforce Health | **Wellness Leagues** | `/fit/workforce-health/wellness-leagues` | list/table | | | |
| 16 | Analyze · Reports | **League Report** | `/fit/leagues` | table, filters, export | | | |
| 17 | Analyze · Reports | **Employee Report** | `/fit/employee-report` | table, filters, export | | | |
| 18 | Analyze · Reports | **Participation Report** | `/fit/participant-report` | table, export | | | |
| 19 | Analyze · Reports | **Incentivisation Report** | `/fit/transaction-report` | table, export | | | |
| 20 | Analyze · Reports | **Wellness Score Report** | `/fit/wellness-score-report` | table, export | | | |
| 21 | Analyze · Reports | **Redemption Report** | `/fit/redemption-report` | table, export | | | |
| 22 | Manage · Rewards | **Upload Points** | `/fit/reward-hub/upload-points` | form, file upload, validation | | | |
| 23 | Manage · Configuration | **Add Employees** | `/fit/configuration/add-employees` | form, file upload | | | |
| 24 | Manage · Configuration | **Preview Emails** | `/fit/configuration/preview-emails` | email templates preview | | | Email copy may be BE-sourced ⭕ |
| 25 | Manage · Configuration | **Settings** | `/fit/configuration/settings` | toggles, labels; **check for a UI-language setting here** | | | |

### Global / persistent chrome (test in every language, logged once)
- Left product rail: Recognition / Wellness / Pulse / Redemption / Milestone / Perks / Admin Hub labels
- Sub-nav group headers: **Challenges, Engage, Analyze, Manage**; expandable sections
- Top banner: company logo, **profile menu** (Open profile menu), **Create** button
- Left rail widgets: **Language** selector, "Active Plan - Grow", **Challenges 1509/∞**, **Licenses 1003**, "Contact Account Manager"
- Overview controls: **All Countries** filter, **This Month** date-range, **"Ask Vantage Fit"** chat (⌘K)
- Badges: **FREE**, **NEW**

### Overlays / states to force per screen (per checklist)
Dialogs & confirmation modals (delete/publish), toasts (success/error), empty states, loading states, validation errors, dropdown/date-picker internals, pagination.

---

## Reachability notes
- All 25 routes above are exposed in the left-nav of the logged-in admin session — none blocked so far.
- "Email Designer" and "Ask Vantage Fit" open overlays/editors; entry to be confirmed on visit.
- Some Reports/Preview-Emails content may be backend-sourced → will mark ⭕ BE-deferred rather than bug when English persists there.
