# Test Cases — Bite-Size Content Builder (Health Bite)

**Ticket:** [VF-2126](https://vantagecirclejira.atlassian.net/browse/VF-2126) — *"[VF_Admin] As an HR I should be able to create Bite Size Content from admin dashboard with preview option."* (Story · status **Testing** · pushed to UAT).

**Entry:** Employee app → profile menu → **HR Admin Dashboard** (opens `dashboard-v2` via a `login-via-token` handshake) → sidebar **Programs → Create Content** (`/fit/programs/on-demand-content?action=create`) → **"Create content"** chooser → **Health Bite** → builder at `/fit/create-bite-size-content`.
> A **direct** deep-link to the builder/dashboard while only the employee app session exists silently redirects to the employee home — see Bug in bug-log (access must go through the HR Admin Dashboard menu first).

**Builder structure (verified):**
- **Step 1 — Languages tab:** pick ≥1 of **18 languages** (English, Arabic, Chinese Simplified, Dutch, French, French Canadian, German, Italian, Korean, Portuguese, Russian, Spanish, Vietnamese, Indonesian, Polish, Hungarian, Hindi, Odia). *"Each gets its own content form."* **Next** is disabled until ≥1 selected.
- **Step 2 — Add Content tab (per language):** a content-level **Title\*** + one or more **pages** (accordions, collapse/expand).
- **Page types** (via **+ Add page**): **Intro Page** (mandatory as Page 1, cannot be removed), **Content Page**, **Quiz Page**.
- **Widgets by page type:**
  - **Intro:** Heading Text\* · Banner\* (image) · Title Text\* · Description\* · Author Card (photo\* + name\* + info\*) · Button\* (CTA)
  - **Content:** Heading Text\* · Banner\* (image) · Title Text\* · Description\* · Button\*
  - **Quiz:** Heading Text\* · Banner\* (image) · Radio Questions (Question\* + ≥2 answers, one correct) · Checkbox Questions (Question\* + ≥2 answers, ≥1 correct) · Button\* — plus **Add component: + Radio Questions / + Checkbox Questions**
- **Button widget** is tagged *"Required — Always present on every page"* (no Remove).
- Footer: **Back · Preview · Save bite-size content.**

**Environment:** UAT — `https://dashboard-v2.vantagecircle.co.in` · VF_Admin (HR Admin Dashboard) · account **meghna.dutta** (region **London**) · Plan "Grow". Driver: **Playwright MCP (Chromium)**. Explored 2026-07-03.

**Legend for Actual Result:** ✓ = verified this run · `(Bug #n)` = defect logged · *Not executed* = case authored, awaiting a QA run.

---

## 1. Access / Entry (`BSC-ACC`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-ACC-001 | Enter builder via HR Admin Dashboard | Logged into employee app as HR admin | Profile menu → HR Admin Dashboard → Programs → Create Content → Health Bite | Builder `/fit/create-bite-size-content` loads with Languages step | Loads correctly via login-via-token handshake ✓ | | P1 |
| BSC-ACC-002 | Direct deep-link without dashboard session | Only employee-app session active | Open `/fit/programs/on-demand-content?action=create` directly | Either loads (if session valid) or prompts login | Silently redirects to employee home `app.../ng/home`, no message (Bug — see bug-log) | | P3 |
| BSC-ACC-003 | RBAC — non-HR/non-admin user | A regular employee account (no HR Admin entry) | Attempt to reach the builder route | Access denied / not offered; cannot create content | **NOT TESTABLE** — need a non-admin UAT account (data gap) | | P1 |
| BSC-ACC-004 | RBAC — HR admin of Company A editing Company B content | Two companies/instances, content in each | Try to open/edit another company's bite-size | Blocked; scoped to own company only | **NOT TESTABLE** — need 2nd company + its content (data gap) | | P1 |
| BSC-ACC-005 | Chooser offers both content types | On Content Library | Click Create | "Linked Content" and "Health Bite" options shown with descriptions | Both shown ✓ | | P3 |

---

## 2. Positive / Happy-path (`BSC-POS`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-POS-001 | Single-language, single Intro page, all fields | Builder open | Select **English** → Next → Title → fill Heading, upload Banner, Title text, Description, Author photo + name + info, Button label → **Save** | Saves; success feedback; item appears in Content Library as Bite Size / 1 language | ✓ **Verified 2026-07-03** — created "QA Smoke — Sleep Basics 2026-07-03"; saved via `createBiteContent` (200); appears in library as Bite Size · 1 language · Health Bites | | P1 |
| BSC-POS-002 | "Next" enables after language pick | Languages step | Select English | "1 selected" shown; Next enabled | ✓ | | P2 |
| BSC-POS-003 | Multi-language content (2+) | Builder open | Select English + French → Next → complete both language forms → Save | Both language forms present; saves; library badge shows "2 languages" | *Not executed* — 18 languages listed, per-language form confirmed | | P1 |
| BSC-POS-004 | Multi-page bite-size (Intro + Content + Quiz) | Add Content step | + Add page → Content Page; + Add page → Quiz Page; fill all → Save | 3 pages saved in order; preview paginates 1→2→3 | Page add + all fields verified; save pending | | P1 |
| BSC-POS-005 | Quiz — radio question, mark correct + mandatory | Quiz page added | Fill Question, 2+ answers, select the correct radio, tick "Mark as mandatory" | Accepts; single correct answer selectable | Fields & controls verified; save pending | | P2 |
| BSC-POS-006 | Quiz — checkbox question, multiple correct | Quiz page added | Fill Question, 3 answers, tick 2 as correct | Accepts multiple correct answers | Controls verified (checkbox multi-select) ✓; save pending | | P2 |
| BSC-POS-007 | Add a 3rd+ answer to a question | Quiz question present | Click **+ Add answer** | New answer row added; delete (×) enabled once >2 | + Add answer present; × disabled at the 2-answer minimum ✓ | | P3 |
| BSC-POS-008 | Add multiple question components to a quiz page | Quiz page | Click **+ Radio Questions** and **+ Checkbox Questions** | Additional question blocks appended | Add-component controls present ✓; add pending | | P3 |
| BSC-POS-009 | Collapse / expand a page accordion | ≥1 page | Click page header ▾/▸ | Toggles the page's widget list | Toggles correctly ✓ | | P3 |
| BSC-POS-010 | Remove a non-Intro page | ≥2 pages | Click **Remove page** on Content/Quiz page | Page removed; numbering re-sequences | Remove page present on Content/Quiz ✓; removal pending | | P2 |
| BSC-POS-011 | Preview opens as mobile preview | Content added | Click **Preview** | Mobile preview panel with page nav (Prev/Next + dots) | Opens ✓ (placeholder copy only — Bug) | | P2 |
| BSC-POS-012 | Preview multi-page navigation | ≥2 pages, preview open | Use Prev/Next/page dots | Navigates between page layouts | Prev/Next + page dots present ✓ | | P3 |
| BSC-POS-013 | Edit an existing bite-size from Library | A saved bite-size exists | Library → row action → Edit | Opens builder pre-filled with saved content | *Not executed* — 9 Bite Size items exist; edit affordance not yet exercised | | P2 |
| BSC-POS-014 | Saved item shows correct language badge | Bite-size saved with N languages | View Content Library row | Badge reads "N language(s)" | Existing rows show 1/2/5/7 languages ✓ (badge feature works) | | P3 |

---

## 3. Negative (`BSC-NEG`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-NEG-001 | Proceed with zero languages | Languages step | Leave all unchecked | Next stays disabled | Next disabled with 0 selected ✓ | | P2 |
| BSC-NEG-002 | Save fully empty form | Add Content step, nothing filled | Click **Save bite-size content** | Blocked; itemized error summary listing every missing field | Blocked ✓ — summary "Please complete the following before saving:" grouped by language, lists Title + each Page·Component·field (Bug refs asterisk issue) | | P1 |
| BSC-NEG-003 | Save with only Title filled | Title entered, widgets empty | Save | Blocked; per-component errors for each page | Validation enumerates each component ✓ | | P2 |
| BSC-NEG-004 | Quiz question with no correct answer marked | Quiz question text + answers, no correct selected | Save | Error: "Mark a correct answer" | Listed in validation summary ✓ | | P2 |
| BSC-NEG-005 | Quiz with fewer than 2 answers | Quiz question | Try to delete down to 1 answer | Prevented; minimum 2 answers | × delete disabled at 2 answers ✓ | | P2 |
| BSC-NEG-006 | Answer option text blank | Answer rows present, text empty | Save | Error: "Answer option text" required | Listed in validation summary ✓ | | P2 |
| BSC-NEG-007 | Whitespace-only in required text fields | Required text fields = spaces only | Enter "   " → Save | Treated as empty; validation fails | *Not executed* — verify trimming | | P2 |
| BSC-NEG-008 | Save some languages complete, others empty | 2 languages, only 1 completed | Save | Per-language validation; blocks on incomplete language | Validation is grouped per language ✓ (behavior on partial-complete pending) | | P2 |
| BSC-NEG-009 | Upload non-image file as Banner/Author photo | Image widget | Upload a `.pdf`/`.txt`/`.exe` | Rejected with clear message | *Not executed* — needs upload run. Note: happy-path upload flow verified = choose file → **Crop photo** modal → Submit → preview | | P2 |
| BSC-NEG-010 | Upload oversized image | Image widget | Upload a very large (e.g. >10 MB) image | Rejected / compressed with message | *Not executed* — size limit unknown (Note/Doubt) | | P2 |
| BSC-NEG-011 | Remove the Button widget | Any page | Attempt to remove the Button component | Not allowed (Button always present) | No Remove on Button; tagged "Required" ✓ | | P3 |
| BSC-NEG-012 | Network failure during Save | Valid form | Throttle/kill network → Save | Clear error; no silent loss; retry possible | *Not executed* — needs network-condition run | | P2 |
| BSC-NEG-013 | Session/token expiry during authoring | Long idle then Save | Save after token expiry | Re-auth prompt; content preserved | *Not executed* — needs long-idle run | | P2 |
| BSC-NEG-014 | Script / HTML injection in text fields | Any text field | Enter `<script>alert(1)</script>` and `<b>x</b>` → Save → view in app/preview | Sanitized/escaped; not executed or rendered as markup | *Not executed* — **security check**, needs save + render | | P1 |

---

## 4. Corner / Boundary (`BSC-COR`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-COR-001 | Select all 18 languages | Languages step | Check every language → Next | 18 per-language forms render; no layout break; usable | *Not executed* — perf/layout at scale | | P3 |
| BSC-COR-002 | Many pages in one language | Add Content | Add ~20 pages | All render; accordions usable; save works; no perf collapse | *Not executed* — scalability | | P3 |
| BSC-COR-003 | Many answers in one question | Quiz question | + Add answer ~20 times | All render; delete works; save works | *Not executed* | | P3 |
| BSC-COR-004 | Very long Title / Heading / Description | Any text field | Paste 5,000+ chars | Enforced max length OR graceful handling; no overflow/crash | *Not executed* — max lengths unknown (Note/Doubt) | | P3 |
| BSC-COR-005 | Emoji & multi-byte unicode | Text fields | Enter `🏃‍♀️🔥`, `日本語`, combining marks → Save → render | Stored & rendered correctly across web + app | *Not executed* | | P3 |
| BSC-COR-006 | RTL language content (Arabic) | Arabic selected | Fill Arabic form; Preview; view in app | RTL layout/alignment correct in form, preview, and app | *Not executed* — **key for future localization** | | P2 |
| BSC-COR-007 | Refresh / navigate away mid-authoring | Partly filled form | Browser refresh or click Back | Draft preserved OR explicit "discard changes?" warning | *Not executed* — **potential data-loss risk; no autosave/draft seen (Note/Doubt)** | | P2 |
| BSC-COR-008 | Duplicate answer text in a question | Quiz question | Two answers with identical text | Allowed or de-duplicated per design; consistent | *Not executed* | | P3 |
| BSC-COR-009 | Duplicate content Title | A bite-size with same title exists | Save another with identical title | Allowed or blocked per design (define expected) | *Not executed* — dup handling unknown (Note/Doubt) | | P3 |
| BSC-COR-010 | Concurrent edit by two admins | Same bite-size open in 2 sessions | Both edit & save | Last-write-wins vs conflict handling defined; no corruption | **NOT TESTABLE** solo — needs 2nd session/account | | P3 |

---

## 5. UI / Design-system (`BSC-UI`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-UI-001 | Required-field asterisk consistency | Add Content | Compare `*` markers across all fields | Every required field shows `*` | **Description Text required on save but shows no `*`** (Bug) — all others show `*` ✓ | | P3 |
| BSC-UI-002 | Preview reflects authored content | Fields filled | Open Preview | Preview shows the actual text/images entered | **Shows placeholder Lorem ipsum only** — "Layout preview only… actual content will appear in the mobile app" (Bug) | | P2 |
| BSC-UI-003 | Preview opens on Page 1 | Multi-page, on page 3 | Click Preview | Preview starts at page 1 | Opened on last page (Next disabled) — minor (Note) | | P4 |
| BSC-UI-004 | Accordion visual states | ≥1 page | Expand/collapse | Clear open/closed affordance (▾/▸); smooth | ▾/▸ toggle works ✓ | | P4 |
| BSC-UI-005 | Disabled-state styling | Languages step / min answers | Observe Next & × when disabled | Visually distinct disabled state | Next & × render disabled ✓ | | P4 |
| BSC-UI-006 | "Required" tag on Button widget | Any page | Observe Button component header | Clear "Required — Always present on every page" chip | Present ✓ | | P4 |
| BSC-UI-007 | Responsive layout (narrow / zoom) | Builder open | Resize to ~1024px & 125% zoom | No overlap/clipping; form + preview usable | *Not executed* | | P3 |
| BSC-UI-008 | Design-system consistency vs rest of dashboard | Builder open | Compare buttons, inputs, spacing, typography with Create Challenge etc. | Matches the design-system components | *Not executed* — **primary design-system rollout check** | | P2 |
| BSC-UI-009 | Empty image placeholder | Image widget, no upload | Observe | Clear "No image" + Upload affordance | "No image" + "Upload image" ✓ | | P4 |
| BSC-UI-010 | Validation summary readability | Empty save | Trigger validation | Grouped, scannable, dismissible, scrolls-to / links to fields | Grouped per language + Dismiss (×) ✓; does not deep-link to fields (enhancement) | | P3 |

---

## 6. Data validation (`BSC-DAT`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-DAT-001 | Trim leading/trailing whitespace | Text fields | Enter `  Title  ` → Save → re-open | Stored trimmed / consistent | *Not executed* | | P3 |
| BSC-DAT-002 | Max-length enforcement per field | Each text field | Exceed limits | Hard cap or validation message per field | *Not executed* — limits undocumented (Note/Doubt) | | P3 |
| BSC-DAT-003 | Image type whitelist | Image widgets | Try jpg/png/webp/gif/svg | Only supported types accepted; message otherwise | *Not executed* — accepted types unknown | | P2 |
| BSC-DAT-004 | Image size / dimension / aspect constraints | Image widgets | Upload tiny, huge, extreme aspect | Constraints enforced or graceful scaling | *Not executed* | | P3 |
| BSC-DAT-005 | Radio requires exactly one correct | Radio question | Save with 0 correct / verify can't pick 2 | Exactly one correct enforced | 0-correct blocked (validation) ✓; single-select radio ✓ | | P2 |
| BSC-DAT-006 | Checkbox requires ≥1 correct | Checkbox question | Save with 0 correct | Blocked: "Mark a correct answer" | Listed in validation ✓ | | P2 |
| BSC-DAT-007 | Minimum 2 answers per question | Any question | Reduce below 2 | Prevented | × disabled at 2 ✓ | | P2 |
| BSC-DAT-008 | Emoji / unicode round-trip | Text fields | Save emoji/CJK/RTL → reopen → app render | Byte-accurate round-trip everywhere | *Not executed* | | P3 |
| BSC-DAT-009 | Special chars & entities | Text fields | `& < > " ' %` and `&amp;` | Stored/rendered literally, not double-escaped | *Not executed* | | P3 |
| BSC-DAT-010 | Category & list thumbnail source | Save a bite-size | Check library row Category + thumbnail | Where are Category (e.g. "Health Bites") & thumbnail set? | ✓ Category **auto-assigned "Health Bites"** (no builder field). Confirm this is always fixed for Bite Size, and where the list thumbnail derives from (banner?) | | P2 |

---

## 7. API / Backend (`BSC-API`)

> The ticket specifies a **structured JSON schema** per page for scalability. These verify the save contract & rendering pipeline. Exact endpoints/payload to be confirmed by capturing the Save network request (dev confirmation needed — Note/Doubt).

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-API-001 | Save issues persist request with full JSON | Valid form | Save with DevTools/network open | One request; payload = languages → pages → components (typed widgets) | ✓ Confirmed endpoint `POST /vantagefit/api/v1/content/createBiteContent` (200) + `POST /api/v1/attachment/upload?source=vf_bite_content_image` per image. Full payload body inspection pending | | P1 |
| BSC-API-002 | Payload matches builder state | Multi-page multi-language | Save, inspect payload | Every page/component/answer + correct-answer flags present & correctly typed | *Not executed* | | P1 |
| BSC-API-003 | Success response → library reflects item | Save succeeds | Observe | Success toast/redirect; new row with correct type + language count + category | Library badge/type render verified on existing rows ✓; new-save flow pending | | P1 |
| BSC-API-004 | Server error handling (5xx/timeout) | Force server error | Save | Graceful error; no data loss; ret/retry | *Not executed* | | P2 |
| BSC-API-005 | Language count integrity | Save N languages | Compare library badge to N | Badge == languages persisted | Existing rows consistent ✓; controlled save pending | | P2 |
| BSC-API-006 | Schema extensibility / versioning | — | Review payload for version field | Schema carries a version for future widgets | *Not executed* — **ask dev if schema is versioned (Note/Doubt)** | | P3 |
| BSC-API-007 | Save vs Publish semantics | Save a bite-size | Determine if Save = live to employees immediately | Defined draft vs publish state | ✓ **Save == publish live** — no draft step; the saved item appeared immediately in the employee Fit → Health bites carousel (count 9→10). Confirm this is intended (no draft/unpublish path seen) | | P1 |
| BSC-API-008 | Auth scope on save (tenant) | HR admin | Inspect request company/tenant scoping | Content bound to caller's company/instance only | *Not executed* — see multi-tenancy suite | | P1 |

---

Cross-cutting factors (multi-tenancy, region/time-zone, cross-platform web+Android, localization future-scope) and **Accessibility** are in [`cross-cutting-and-platform.md`](./cross-cutting-and-platform.md). The end-to-end critical path is in [`smoke-suite.md`](./smoke-suite.md).
