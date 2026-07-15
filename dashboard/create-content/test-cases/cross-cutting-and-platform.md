# Test Cases — Bite-Size Content: Cross-cutting, Platform & Accessibility

**Ticket:** [VF-2126](https://vantagecirclejira.atlassian.net/browse/VF-2126). Companion to [`bite-size-content-builder.md`](./bite-size-content-builder.md).

These cover the factors that span beyond the authoring form itself: **multi-tenancy** (companies live on different servers/instances), **region & time-zone** (users of one company span regions/TZs), **cross-platform** (bite-size content surfaces in **web app + Android app** after creation), **localization** (future-scope), and **accessibility**.

**Key context to establish before running (from dev/product):**
- Which UAT **instance/server + company** the authored content is scoped to, and a **second** company/instance to prove isolation.
- Whether **Save == publish live** to employees, or there is a draft/publish/schedule step (see BSC-API-007).
- The **employee-facing surfaces**: web app path and Android app screen where Bite Size appears.

**Legend:** ✓ verified · *Not executed* authored/pending · **NOT TESTABLE** blocked (needs data/2nd account/other driver).

---

## 8. Multi-tenancy (`BSC-TEN`)

> Companies are created on various servers/instances; users are added per instance. Content must not leak across tenants.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-TEN-001 | Content scoped to creating company | HR admin of Company A | Create+save a bite-size on Company A | Item visible only under Company A's library & employees | **NOT TESTABLE** solo — needs a 2nd company to compare (data gap) | | P1 |
| BSC-TEN-002 | No cross-tenant visibility (library) | Content exists in Company A | Log into Company B admin dashboard → Content Library | Company A's bite-size is absent | **NOT TESTABLE** — needs 2nd company account | | P1 |
| BSC-TEN-003 | No cross-tenant visibility (employees) | Content in Company A | Employee of Company B opens app | Cannot see Company A content | **NOT TESTABLE** — needs 2nd company employee | | P1 |
| BSC-TEN-004 | Cross-instance/server isolation | Companies on different servers | Create on server-1 company; check server-2 | Fully isolated; no bleed; IDs don't collide | **NOT TESTABLE** — needs multi-server access | | P1 |
| BSC-TEN-005 | RBAC across tenants | HR admin A | Try to open/edit Company B's content by ID/URL | Denied (403 / not found) | **NOT TESTABLE** — needs 2nd tenant content ID | | P1 |
| BSC-TEN-006 | Feature-flag / plan gating per company | Company without the feature/plan | Check builder availability | Hidden or gated appropriately | *Not executed* — this account is Plan "Grow" with feature on | | P2 |

---

## 9. Region & Time-zone (`BSC-TZ`)

> One company can have users across regions & time zones (this admin's region = **London**). Verify dates/scheduling render per viewer.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-TZ-001 | Created/updated timestamp correctness | Bite-size saved | Note save time; view library "date" (rows show e.g. "Friday 26 Jun") | Date reflects correct TZ; consistent server vs client | *Not executed* — confirm which TZ library dates use | | P2 |
| BSC-TZ-002 | Publish/schedule respects viewer TZ | If scheduling exists | Schedule content; view as users in IST, GMT, PST | Goes live at the intended instant for each viewer; no off-by-a-day | *Not executed* — **scheduling field not seen in builder (Note/Doubt — does it exist?)** | | P2 |
| BSC-TZ-003 | Date display for different-region users | Same company, users in ≥2 TZs | Compare visible dates in app | Localised, correct, no negative/duplicate day | **NOT TESTABLE** — needs users in different TZs | | P3 |
| BSC-TZ-004 | DST / date-boundary edge | Save near midnight / DST switch | Observe date shown | Correct across DST & midnight boundaries | *Not executed* | | P3 |
| BSC-TZ-005 | Admin region vs company region | Admin London, company elsewhere | Save & compare timestamps | Consistent, documented reference TZ | *Not executed* — account region = London ✓ (baseline noted) | | P3 |

---

## 10. Cross-platform — Web app + Android app (`BSC-XP`)

> Bite-size content is authored on the dashboard and consumed in the **employee web app and Android app**. Android verification uses **mobile-mcp (separate driver)** — flagged as a handoff.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-XP-001 | Bite-size appears in employee **web** app | Content saved/published to a company | Log into web app as that company's employee → wellness/content area | Content visible & openable | *Not executed* — needs save + employee view | | P1 |
| BSC-XP-002 | Bite-size appears in **Android** app | Same as above | Open Android app (mobile-mcp) → content area | Content visible & openable | **HANDOFF** — mobile-mcp driver (Android), not this Playwright run | | P1 |
| BSC-XP-003 | Intro page renders (web + Android) | Intro authored | Open the bite-size | Heading, banner, title, description, author card, CTA all render correctly | *Not executed* — parity check | | P1 |
| BSC-XP-004 | Content page renders (web + Android) | Content page authored | Open & navigate | Heading, banner, title, description, CTA render | *Not executed* | | P1 |
| BSC-XP-005 | Quiz page — radio (single-select) | Quiz authored | Answer a radio question | Single selection; correct/incorrect handled; mandatory enforced | *Not executed* | | P2 |
| BSC-XP-006 | Quiz page — checkbox (multi-select) | Quiz authored | Answer a checkbox question | Multiple selection; scoring per correct set | *Not executed* | | P2 |
| BSC-XP-007 | "Mark as mandatory" enforced in app | Mandatory question | Try to proceed without answering | Blocked until answered | *Not executed* | | P2 |
| BSC-XP-008 | Multi-page navigation & CTA in app | Multi-page bite-size | Tap Continue/Next through pages | Paginates correctly; final CTA behaves | *Not executed* | | P2 |
| BSC-XP-009 | Preview-vs-actual parity | Authored content | Compare dashboard Preview layout to real app render | Layouts match (given preview is layout-only) | *Not executed* — **note preview shows placeholders (Bug), so only layout can be compared** | | P2 |
| BSC-XP-010 | Correct language shown per user | Multi-language bite-size | Open as users with different app languages | User sees their language; fallback when absent | *Not executed* | | P2 |
| BSC-XP-011 | Image rendering & scaling in app | Banner/author images uploaded | Open on various screen sizes | No distortion/crop issues; loads reliably | *Not executed* | | P3 |
| BSC-XP-012 | Edit on dashboard reflects in app | Published bite-size edited | Edit + save → reopen in app | Update propagates (define cache/refresh expectations) | *Not executed* | | P2 |
| BSC-XP-013 | Language preference drives the content language shown | Bite-size authored in ≥2 languages **including** the viewer's app language; content available/published to the company | A user in a given **region** with app language **X** opens the bite-size | Content is shown in the user's app language **X first** when that language exists for the item; otherwise a sensible fallback | *Not executed* — **explicitly requested case.** Needs users with different app languages set. Ties to BSC-L10N-003 (fallback) | | P2 |
| BSC-XP-014 | Open a published bite-size in the employee **web** viewer | A published bite-size exists | Fit → Programs → Library → Health bites → open a bite | Viewer renders all pages (heading, banner, title, description, author, CTA / quiz) | ❌ **FAILS — Bug #6 (2026-07-03):** viewer opens **blank** (broken-content icon); content images 404 with malformed URLs (double `.png.png`, duplicated path segments, `default.png` 404) + `X-Frame-Options: deny`. Reproduced on pre-existing bites too | | P2 |

---

## 11. Localization — future scope (`BSC-L10N`)

> Content authoring already supports 18 languages. This is the **future localization test scope** (both authored-content rendering and builder-UI localization). Ties to the web-dashboard localization plan in `dashboard/localization/`.

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-L10N-001 | Each of 18 languages renders in app | Content authored per language | View content as each locale | Correct script, fonts, no tofu/□ glyphs | *Not executed* — **future scope** | | P2 |
| BSC-L10N-002 | RTL languages (Arabic) end-to-end | Arabic content | Author → preview → app | RTL layout correct in form, preview, and app | *Not executed* — **future scope** | | P2 |
| BSC-L10N-003 | Missing-language fallback | Bite-size lacks user's language | Open as that user | Sensible fallback (e.g. English) not blank/error | *Not executed* — **future scope** | | P2 |
| BSC-L10N-004 | Builder UI localization | Dashboard set to non-English | Open builder | UI labels localized (or documented English-only) | *Not executed* — builder labels currently English | | P3 |
| BSC-L10N-005 | Language list completeness & naming | Languages step | Compare offered languages to app-supported locales | List matches supported locales; names correct | 18 languages listed ✓ (parity vs app locales to confirm) | | P3 |
| BSC-L10N-006 | Per-language independent validation | Multi-language | Complete some languages, not others | Validation isolates per language | Validation grouped per language ✓ | | P2 |

---

## 12. Accessibility (`BSC-A11Y`)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| BSC-A11Y-001 | Icon-only buttons have labels | Builder + Library | Inspect accessible names | Every control has a meaningful name | **Some icon-only buttons expose empty names** (e.g. Library row action/kebab) (Bug) | | P3 |
| BSC-A11Y-002 | Form fields have programmatic labels | Add Content | Screen-reader focus each field | Label announced (Title, Heading text, Answer, etc.) | Text fields labeled ✓; some answer inputs use generic "Answer" (ok) | | P3 |
| BSC-A11Y-003 | Radio/checkbox groups announce role & correct-marker | Quiz | SR focus answers | Group role + "correct answer" toggle announced | "Correct answer" radio/checkbox exposed ✓ | | P3 |
| BSC-A11Y-004 | Keyboard-only authoring | Builder | Tab through: select language, add page, fill, save | Full keyboard operability; visible focus | *Not executed* | | P2 |
| BSC-A11Y-005 | Validation error announcement | Empty save | With SR on, trigger validation | Error summary announced (role=alert / focus moved) | *Not executed* — summary is visible; SR semantics to confirm | | P3 |
| BSC-A11Y-006 | Color contrast (text, disabled, chips) | Builder | Check contrast ratios | Meet WCAG AA (4.5:1 text) | *Not executed* | | P3 |
| BSC-A11Y-007 | Touch-target size (dashboard on touch) | Builder | Measure ×, dots, small icons | ≥44×44px | *Not executed* — answer × and preview dots look small | | P3 |
