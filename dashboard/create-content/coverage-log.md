# Coverage Log — Web Dashboard / Create Content

What was tested / partial / blocked / skipped, per run. Append per run — never overwrite.

**Legend:** ✅ Done · 🟡 Partial · ⬜ Not started · ⛔ Blocked (environment) · 🚫 Excluded (by request)

| Run | Date | Module / Screen | Browser | Status | Notes |
|---|---|---|---|---|---|
| 1 | 2026-07-03 | VF-2126 Bite-Size Content Builder — structure & authoring flow | Chromium (Playwright MCP) | 🟡 | Full builder mapped; test cases + smoke + bugs authored. E2E save/publish & Android pending |

---

## Run 1 — VF-2126 Bite-Size Content Builder (2026-07-03, UAT)

**Environment:** `https://dashboard-v2.vantagecircle.co.in` · HR Admin Dashboard · account meghna.dutta (region London) · Plan Grow · Chromium via Playwright MCP.

**Covered (✅ verified this run):**
- Access path: employee app → profile menu → HR Admin Dashboard (login-via-token) → Programs → Create Content → Health Bite → builder.
- Create chooser (Linked Content vs Health Bite).
- Languages step: 18 languages; ≥1 required; "Next" gating.
- Add Content step: content Title; 3 page types (Intro locked as Page 1, Content, Quiz) via + Add page.
- All widgets per page type: Heading Text, Banner (image), Title Text, Description, Author Card, Button; Quiz Radio/Checkbox questions (min 2 answers, correct-answer marking, mark-mandatory, + Add answer, + add component).
- Page accordion collapse/expand; Remove page (non-Intro); Button always-present rule.
- Preview ("Mobile Preview") open + pagination — placeholder-only (Bug #1).
- Empty-form Save validation (grouped per language, itemized per page/component).

**Deliverables produced:**
- `test-cases/bite-size-content-builder.md` — Access, Positive, Negative, Corner, UI/Design-system, Data-validation, API/Backend.
- `test-cases/cross-cutting-and-platform.md` — Multi-tenancy, Region/TZ, Cross-platform (web+Android), Localization (future), Accessibility.
- `test-cases/smoke-suite.md` — 11-case critical-path suite.
- `bug-logs/bug-log.md` — 5 findings + Notes/Doubts + Senior-QA suggestions.
- `evidence/01`…`07` screenshots.

**NOT done / gaps (and why):**
- 🟡 **End-to-end save + publish** — not executed. Requires uploading real images (Banner*, Author photo*) and creating live UAT content. Structure & validation verified; happy-path save (SMOKE-004/007, BSC-POS-001) still pending.
- ⛔ **Android app verification** (BSC-XP-002/…) — needs **mobile-mcp** (separate driver); handoff.
- ⛔ **Employee web-app surfacing** (BSC-XP-001, SMOKE-009) — needs an employee view of the target company.
- ⛔ **Multi-tenancy / cross-instance** (BSC-TEN-*) — needs a **2nd company/instance + accounts**.
- ⛔ **Region/time-zone across users** (BSC-TZ-003) — needs users in different TZs.
- ⬜ **Image validation** (type/size/dimension), **max-length limits**, **network-failure & session-expiry**, **injection/XSS**, **keyboard & contrast a11y**, **design-system parity** — authored, not executed.
- ⬜ **Localization** (BSC-L10N-*) — future scope, per request.
- ❓ Open product/dev questions blocking definitive expected-results: Save-vs-Publish, Category/thumbnail source, autosave/draft, scheduling, schema versioning (see bug-log Notes/Doubts).

---

## Run 1b — Live end-to-end publish (2026-07-03, UAT) ✅

Authorized live UAT publish executed. **Created a real item:** **"QA Smoke — Sleep Basics 2026-07-03"** (single Intro page, English) — left in the UAT library, clearly labelled, optional cleanup.

**Verified end-to-end:**
- Authoring: all Intro text fields + 2 image uploads. **Image upload flow** = click Upload → OS file chooser → **"Crop photo"** modal → **Submit** → "preview" thumbnail + "Replace image".
- **Page removal is guarded** by a confirm dialog ("…This action cannot be undone").
- **Save** → `POST /vantagefit/api/v1/content/createBiteContent` (200) + `POST /api/v1/attachment/upload?source=vf_bite_content_image` ×2 (200) → redirect to Content Library.
- **Content Library:** new row Bite Size · **1 language** · Category **Health Bites** (auto-assigned; no builder field). Overview count **9 → 10**.
- **Employee web app** (Fit → Programs → Library → **Health bites**): carousel now shows **10** bites (= dashboard total) → **content is live to employees immediately (Save == publish)**.

**Answered open questions:** Save == publish live (no draft step); Category auto-assigned "Health Bites".

**Still pending:** open the exact employee slide to verify intro-page render fidelity; Android app (mobile-mcp handoff); multi-tenant isolation (2nd company); full Save payload body inspection; image type/size validation.
