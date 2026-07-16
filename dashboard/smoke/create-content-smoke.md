# Smoke Suite — Bite-Size Content (Health Bite) Module

**Ticket:** [VF-2126](https://vantagecirclejira.atlassian.net/browse/VF-2126). Run this **first** after every deploy to UAT — if any P1 here fails, stop and reject the build before deeper testing.

**Scope:** the critical create → preview → save → surfaced-to-users path. **~10 cases, target < 15 min.**
**Environment:** UAT `https://dashboard-v2.vantagecircle.co.in` · HR Admin Dashboard. Never run destructive publish on production.
**Test data:** Title `Smoke Test — Sleep Basics {date}` · Heading `Sleep Better Tonight` · Title text `Wind Down` · Description `Three simple habits for deeper sleep.` · Author `QA Bot` / `Wellness Team` · Button `Start` · Quiz Q `How many hours of sleep are recommended?` answers `5 / 7-9 (correct) / 12` · Banner + author image: any valid JPG/PNG.

**Legend:** ✓ verified this run · *Not executed* pending · **HANDOFF** other driver.

| Test Case ID | Description | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|
| SMOKE-001 | Builder reachable | Profile → HR Admin Dashboard → Programs → Create Content → Health Bite | Builder loads at `/fit/create-bite-size-content`, Languages step | Loads ✓ | | P1 |
| SMOKE-002 | Language selection gates Next | Select English | "1 selected"; Next enabled | ✓ | | P1 |
| SMOKE-003 | Add all three page types | Next → + Add page → Content; + Add page → Quiz | Intro (locked as P1) + Content + Quiz added, in order | ✓ (page add + types verified) | | P1 |
| SMOKE-004 | Complete all required fields | Fill Title + every widget on all pages (upload banner + author image), quiz Q with a correct answer | All fields accept input; no blocking errors | ✓ verified for a single Intro page (2026-07-03): all text fields + 2 image uploads accepted. Image flow = choose file → **Crop photo** modal → **Submit** → "preview" thumbnail | | P1 |
| SMOKE-005 | Empty-form validation blocks save | On an empty form, click Save | Save blocked; itemized error summary per language/page/component | ✓ (validation summary verified) | | P1 |
| SMOKE-006 | Live preview opens & paginates | Click Preview; use Prev/Next/dots | Mobile preview opens; navigates pages 1→3 | ✓ opens & paginates (placeholder copy — Bug, non-blocking) | | P2 |
| SMOKE-007 | Save succeeds | Click Save bite-size content on a valid form | Success feedback; builder closes/redirects; no error | ✓ (2026-07-03) `POST /vantagefit/api/v1/content/createBiteContent` → 200; 2× `POST /api/v1/attachment/upload?source=vf_bite_content_image` → 200; redirected to Content Library | | P1 |
| SMOKE-008 | Appears in Content Library | After save, open Content Library | New row: type **Bite Size**, correct **N language** badge, category | ✓ Row "QA Smoke — Sleep Basics 2026-07-03" · Bite Size · 1 language · Category **Health Bites** (auto-assigned); Overview count 9→10 | | P1 |
| SMOKE-009 | Surfaces in employee **web** app | As an employee of that company, open the content area (Fit → Programs → Library → Health bites) | Bite-size visible & openable; pages render | ✓ Employee Fit → Programs → **Health bites** carousel now shows **10** bites (= dashboard total); item live immediately. Opening the exact slide to verify page render = quick follow-up | | P1 |
| SMOKE-010 | Surfaces in **Android** app | Open Android app content area | Bite-size visible & openable; quiz interactive | **HANDOFF** — mobile-mcp (Android) | | P1 |
| SMOKE-011 | Multi-language save | Repeat SMOKE-004 with English + one more language | Both saved; library badge shows "2 languages" | *Not executed* | | P2 |
