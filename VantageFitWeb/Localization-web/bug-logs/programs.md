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

## Assignment (this run)
- Frontend: B12 recurrences (register); B13 ("Written By"); B15 (needs confirmation before assigning).
- Backend: B14 (empty View-all grid) — needs confirmation whether locale-specific.
