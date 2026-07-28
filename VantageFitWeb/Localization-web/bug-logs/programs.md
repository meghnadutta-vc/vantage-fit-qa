# Vantage Fit Web — Programs module — Localization bug log

**Surface:** `/ng/fit/programs` · Account anjan.pathak@… (UAT) · Executed 2026-07-24 (en baseline + de).
**Evidence:** `../evidence/programs_en_baseline.png`, `../evidence/programs_de.png`.

**Summary:** Programs FE chrome localizes well in German (subtitle, Library/Offerings sub-tabs, Health-bites
header + "15-30 Sek. Tipps…", "Alle anzeigen", footer, motivational tagline). No NEW Programs-specific FE
defect. Findings:

### Recurs: B3 — "Challenges" nav tab not translated in German
Same as Summary/Challenges. Evidence: `../evidence/programs_de.png` (tab bar).

### NEW: B11 — Language preference not persisted across sessions (reverts to English)
```
[Localization / Functional (FE/BE) - P2]
[Profile language ↔ session — preference resets to English on re-login]
After the browser session expired and I re-logged in, the account language had reverted to English even
though it had been saved as German earlier (and German had rendered correctly that session).

Expected: a saved language preference persists across logout/login until the user changes it.
Actual: profile "Language" read back as "English" after a fresh login; the Fit web loaded in English
  (html lang="en") despite German having been saved. Re-selecting German + re-login restored German for
  that session only.
Note/Doubt: could be (a) language stored session-only, not persisted to the account, or (b) a default-to-
  English on session bootstrap. Needs dev confirmation of intended persistence. Reproduced once cleanly
  (natural session expiry → re-login → English). [FE/BE — TBD]
Evidence: profile "Edit Profile → Language" select read "English"; programs_en_baseline.png loaded in EN.
```

### Observation (not a bug): content is language-scoped
The Programs **Library** content differs by locale — English shows Featured Content + Exercise/Healthy
Eating/Mindfulness carousels (many items); German shows only Health-bites with one localized item
("Vollständiger Leitfaden für gesunde Ernährung"). This is backend/content-population, not a translation
defect. → classify [BE / content data]. Flag as a **content-coverage** gap for non-English locales.

### Copy (verify owner): English category typos
English category headers "**Excercise**" and "**Mindfuless**" are misspelled (should be "Exercise",
"Mindfulness"). Likely content-category master data rather than FE i18n — confirm with content owner. P4.
Evidence: `../evidence/programs_en_baseline.png`.

## Assignment
- Frontend: B3 (German "Challenges" tab) — already tracked; B11 persistence (FE or BE — TBD).
- Backend/content: language-scoped content coverage; category-label typos (if data).

---

## Offerings sub-tab + content detail pages (2026-07-28, German)
**Evidence:** `../evidence/programs_de_offerings_tab.png`, `../evidence/programs_de_viewall_empty_modal.png`,
`../evidence/programs_de_bitecontent_detail_overlap.png`, `../evidence/programs_de_bitecontent_step2.png`.

**Summary:** Offerings chrome (filters, category labels, "Partner-Angebote" heading) and the bite-size
content detail flow both localize well overall, but surface **two recurrences of B12** (register mixing —
now confirmed in Offerings' intro line AND inside authored content body copy, not just top-level chrome),
plus **three new findings**: an untranslated "Written By" label, an empty "View all" content grid, and a
CTA-button/text overlap in the bite-size intro screen.

### Recurs: B12 — formal/informal register mixing (2 new surfaces)
- Offerings sub-tab subtitle: "**Um Ihre umfassenden Wellness-Bedürfnisse zu erfüllen**" — formal *Ihre*.
- Bite-size content intro body: "**Ihren** Körper mit den richtigen Nährstoffen…" — formal *Ihren*, inside
  authored article copy (extends B12's scope beyond UI chrome into content-body text).
Evidence: `../evidence/programs_de_offerings_tab.png`, `../evidence/programs_de_bitecontent_detail_overlap.png`.

### NEW: B13 — "Written By" label not translated in bite-size content detail
```
[Localization - P3]
[Programs → Health-bites → content detail dialog — author byline]
The bite-size content detail dialog ("Vollständiger Leitfaden für gesunde Ernährung") is otherwise fully
localized into German, but the byline label stays English.

Expected: "Written By" renders in German (e.g. "Geschrieben von").
Actual: label shows "Written By" in the German-rendered dialog; only the label is English — the value
  ("Vantage Fit Team") is correctly left as-is (proper noun).
Note/Doubt: could not confirm FE-dictionary status — the /assets/i18n/fit/de.json fetch returns the SPA
  HTML shell (known infra issue, see B10), so key-lookup classification wasn't possible; likely a hardcoded
  FE string given every other string on this exact dialog translates. [FE — likely, TBD to confirm via
  bundle search]
Evidence: ../evidence/programs_de_bitecontent_detail_overlap.png
```

### NEW: B14 — Health-bites "Alle anzeigen" (View all) opens an empty grid
```
[Functional / Backend - P2]
[Programs → Health-bites → "Alle anzeigen" modal]
Clicking "Alle anzeigen" opens a modal titled "Gesundheitstipps" (translated correctly) but the content grid
inside is empty, even though German content for this category exists and renders elsewhere on the same page.

Expected: the modal lists the German health-bites content (the same items the homepage carousel shows).
Actual: modal body is empty (`<div class="library-modal-grid">` renders nothing). Verified via network:
  GET /content/category/20?page=0&perPage=12 → 200 OK but body `{"data":{"data":[]}}`. The SAME categoryId
  (20) returns 2 German items via the POST /content/byCategoryName endpoint (used by the homepage carousel):
  "Vollständiger Leitfaden für gesunde Ernährung" and "Schlaf verstehen: Warum Schlaf für die allgemeine
  Gesundheit wichtig ist" — so the content demonstrably exists for this locale; the paginated endpoint just
  doesn't return it.
Note/Doubt: not yet confirmed whether this reproduces in English (would indicate a general API bug
  unrelated to language) or is German-specific (a locale param not passed/handled correctly on the
  paginated endpoint). Needs an English-baseline comparison and dev confirmation. [BE — likely, TBD]
Evidence: ../evidence/programs_de_viewall_empty_modal.png
```

### NEW: B15 — CTA button overlaps body text in bite-size content intro screen
```
[UI - P3]
[Programs → Health-bites → bite-size content detail, step 1 ("Einführung")]
The "Fangen wir an" CTA button renders visually in the middle of the intro paragraph, splitting one sentence
into two halves above and below the button, inside the phone-frame preview (`.bite-device` container).

Expected: CTA sits below/after the body text with clear separation, not overlapping or interrupting it.
Actual: button (`position: static`, no transform/negative margin found) sits between two text fragments of
  the same paragraph in visual order; the surrounding scroll container does not overflow (scrollHeight ==
  clientHeight), so this isn't a translation-length overflow in the classic sense — cause not confirmed.
Note/Doubt: root cause unconfirmed (verified DOM order and computed styles; could not isolate why the visual
  position interleaves with text — may need an English-baseline comparison to rule out a language-agnostic
  template bug vs. German-text-length trigger). Flagging as UI, not localization, pending confirmation.
Evidence: ../evidence/programs_de_bitecontent_detail_overlap.png
```

### Documented behavior (not a bug): Offerings cards redirect externally
Clicking a partner-offer card (e.g. "Decathalon") opens the partner's own website in a new tab — there is no
in-app "detail page" for Offerings. Out of scope for this app's localization (external site).

---

## Spanish cross-check (2026-07-28)
**Evidence:** `../evidence/programs_es_bitecontent.png`.

Re-ran the Offerings + bite-size-content-detail flow in Spanish specifically to resolve the open
Note/Doubts on B12, B13, B14, B15. Results:
- **B12 recurs** — "Para cuidar de **sus** necesidades de bienestar completa" (Offerings subtitle) carries
  the identical formal-register slip as German's "Ihre umfassenden…", in the exact same structural position.
- **B13 recurs identically** — "Written By" stays English in the Spanish bite-size content dialog too, with
  every other string on that dialog translating. Confirms hardcoded FE string, not a per-locale missing key.
- **B15 confirmed language-independent** — the CTA-overlap bug reproduces pixel-for-pixel in Spanish with a
  different-length body paragraph, ruling out "German text is too long" as the cause. This is a template bug.
- **B14 confirmed German-specific** — the same "Ver todo" (View all) flow in Spanish returns a **populated**
  grid (3 items), not empty. The bug is isolated to German's locale handling on that one endpoint.
- **New observation (not a bug):** the Spanish Library carousel/grid surfaces content titled literally
  "Spanish Content" and "New SPANISH Updated English Content" — these read as QA/test placeholder titles
  left in the CMS rather than genuine health content. Flagging for the content owner as a data-quality note,
  not a translation defect (title text is BE/content data).

---

## Missed on the first pass — found via a second review (2026-07-28, prompted by user)

### NEW: B23 — Content thumbnails render as solid black boxes (malformed CDN image URLs)
```
[Functional / Backend (data) - P2]
[Programs → Library (Health-bites carousel + Excercise cards) and Offerings (partner cards)]
28 unique image requests 404 on a single page load: 23 with a doubled ".png.png" extension, 1 with a
doubled path segment ("VantageFit/content_image/VantageFit/content_image/..."), 4 genuinely missing named
assets (clean URLs, e.g. "bite-contents/sleep-management/sleep-management-01.png"), plus the fallback image
itself ("content_image/default.png") also 404ing 7 times — so broken thumbnails have no working placeholder
either. Visually this renders as solid black boxes across nearly every visible thumbnail on Library, and
several cards on Offerings.

Expected: thumbnails load their real image, or fall back to a working placeholder.
Actual: ~all Library thumbnails and multiple Offerings cards show as blank black squares.
Note/Doubt: this was originally NOTICED during the initial German pass (visible in
  programs_de_offerings_tab.png as a large black box) but never logged — caught on a second review. URLs
  contain no locale segment, so this is language-independent. [BE]
Evidence: ../evidence/programs_library_broken_images.png, ../evidence/programs_de_offerings_tab.png
```

### NEW: B24 — Offerings tab intermittently shows "Unable to load offerings right now"
```
[Functional - P3]
[Programs → Offerings tab]
Observed the Offerings tab show an explicit error state (icon + "Unable to load offerings right now." +
"Try again" button) instead of the partner-offer grid, coinciding with a 502 Bad Gateway on
GET /vantagefit/api/v1/marketplace/categories. Clicking "Try again" recovered it on the next attempt.

Expected: Offerings loads reliably.
Actual: at least one 502 observed during testing; recovered cleanly on manual retry.
Note/Doubt: appears to be intermittent backend flakiness, not a permanent failure — only seen once across
  many page loads today. The existing error-state + retry UX is reasonable; flagging as a backend
  reliability note for the /marketplace/categories endpoint. [BE]
Evidence: ../evidence/programs_offerings_unable_to_load.png
```

---

## Effective-language desync (2026-07-28 deep-dive) — see B25 in the consolidated log
Category/Subcategory filters on Offerings work correctly (functionally verified — selecting "Físico"
narrows the grid, empty state renders when no matches). But re-opening Library later in the same Spanish
session (no re-login, no language change) served the **full English-baseline content set** instead of the
Spanish-scoped 2-item set seen earlier that day — confirming the content-fetch API itself, not just FE
chrome, is subject to the runtime-language desync documented in **B25**. This is Programs' clearest evidence
that B25 affects backend queries, not only translated UI strings.

## Assignment (this run)
- Frontend: **B12** (confirmed cross-language, register fix at source-string level recommended); **B13**
  (confirmed cross-language, hardcoded FE string); **B15** (confirmed cross-language, FE layout/template bug).
- Backend: **B14** (confirmed German-specific — locale-handling bug on the paginated content endpoint);
  **B23** (new, P2 — malformed content-image URLs, highest priority of this module's backend findings);
  **B24** (new, P3 — intermittent 502 on marketplace/categories); **B25** (new, P2 — effective-language
  desync, confirmed here to also affect content queries, not just chrome).
- Content/data (FYI, not a bug): placeholder-looking Spanish library titles ("Spanish Content" etc.).

## French cross-check (2026-07-28) — confirms, no new bugs
- **B23 (broken images)** reproduces identically — same black-box thumbnails on Offerings.
- **B14 (empty "View all" grid) does NOT reproduce** — French returned 2 populated items, matching Spanish
  and further confirming B14 is German-specific.
- **B12 register**: Offerings subtitle "Pour répondre à l'ensemble de **vos** besoins en matière de
  bien-être." — formal "vos", same structural position as German "Ihre"/Spanish "sus" — 3rd language on
  this exact surface.
- Category/subcategory filter chrome was English at time of testing (session-wide B25 state); not
  separately functional-tested in French (already verified functional in Spanish).

## Portuguese cross-check (2026-07-28) — confirms B23, complicates B14
- **B23 (broken images)** reproduces identically — 4th language confirmation.
- **B14 result is ambiguous, not a clean recurrence:** the "View all" grid returned 0 items in Portuguese —
  but the main Library carousel was simultaneously showing the full English-baseline content set (confirming
  B25 was active at the time), and no per-request locale header/param exists on this call (the backend
  resolves language from server-side session state). This result can't be cleanly attributed to Portuguese
  specifically — see the consolidated log's updated B14 note. Needs a clean-session re-test.
- **B12 register**: Offerings subtitle "Para cuidar de **suas** necessidades abrangentes de bem-estar" uses
  the standard "você"-based possessive — see the judgment-call note in `challenges.md`/consolidated log:
  not confirmed as register mixing for Portuguese specifically (no competing "tu"-form found).
