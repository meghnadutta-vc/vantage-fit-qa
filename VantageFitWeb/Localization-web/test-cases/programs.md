# Vantage Fit Web — Programs module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/programs` (tab "Programs"). Sub-tabs: **Library / Offerings**.
**Account:** anjan.pathak@… (UAT). **Executed:** 2026-07-24 — English baseline + German (de); **2026-07-28 —
Spanish (es) added** (Library, Offerings, content detail, View-all); fr/pt still pending.
**Evidence:** `../evidence/programs_en_baseline.png`, `../evidence/programs_de.png`, `../evidence/programs_es_bitecontent.png`.

## English baseline — screen inventory
- Nav tabs + `+ Add`. Subtitle: "Library content and wellness offerings."
- Sub-tabs: **Library / Offerings**.
- Sections (content-driven): "Featured Content" → "Curated Health Content for you"; "Health bites" → "15-30 sec tips"; category carousels "Excercise" *(typo)*, "Healthy Eating", "Mindfuless" *(typo)* — each with content cards.
- Content cards = user/BE-authored titles ("Managing Workplace Stress…", "Top 10 Exercises To Build Muscle", "sdfdsf", "Test Conetnt Title", etc.).
- Footer: "Scan to sign in on your phone", "© 2026 Vantage Fit…", "Need Help with Vantage Fit?"

## Test cases

| Test Case ID | Description | Steps | Expected | Actual (de) | Status | Priority |
|---|---|---|---|---|---|---|
| PRG-LOC-001 | Subtitle localized | Switch lang → open Programs | Translated | "Bibliotheksinhalte und Wellness-Angebote." ✅ | PASS | P2 |
| PRG-LOC-002 | Sub-tabs Library/Offerings localized | Read sub-tabs | Translated | "Bibliothek" / "Angebote" ✅ | PASS | P2 |
| PRG-LOC-003 | "Health bites" + "15-30 sec tips" localized | Read section header | Translated | "Gesundheitstipps" + "15-30 Sek. Tipps für ein gesünderes Leben." ✅ | PASS | P2 |
| PRG-LOC-004 | "View all" localized | Read CTA | Translated | "Alle anzeigen" ✅ | PASS | P3 |
| PRG-LOC-005 | Motivational background tagline localized | Read faded bg text | Translated | "Jetzt schwitzen, später glänzen" ✅ | PASS | P3 |
| PRG-LOC-006 | Footer localized | Read footer | Translated | "Scanne, um dich…", "© 2026 Vantage Fit. Entwickelt für gesündere Teams.", "Brauchst du Hilfe…" ✅ | PASS | P3 |
| PRG-LOC-007 | "Challenges" nav tab localized (de) | Read tab bar | "Herausforderungen" | Shows "Challenges" → **Bug B3 recurs** | FAIL (de) | P2 |
| PRG-LOC-008 | "Featured Content" / "Curated Health Content for you" section headers localized | Read section headers | Translated | **Not verifiable in de** — section absent (no featured items for de); needs a locale with featured content | Needs Verification | P2 |
| PRG-LOC-009 | Content cards = BE/authored data (not FE i18n) | Read card titles | Stay as authored per locale | Content is **language-scoped**: de Library shows ~1 localized bite ("Vollständiger Leitfaden für gesunde Ernährung") vs en's many items → BE/content-population, expected | PASS (classified BE data) | — |
| PRG-LOC-010 | `<html lang>` correct | Read lang attr | Matches | de→"de" ✅ | PASS | P3 |
| PRG-LOC-011 | Category labels "Excercise"/"Mindfuless" (EN typos) | Read en category headers | Correct spelling | EN shows misspelled "Excercise" & "Mindfuless" (likely content-category data) → copy issue, verify owner | Needs Verification | P4 |

## Notes / pending
- **Content is language-scoped** (backend): English has a full library (Featured + Exercise/Healthy Eating/Mindfulness carousels); German shows only Health-bites with one localized item. Not a translation defect — flag as content-population coverage.
- fr/es/pt passes pending (FE chrome expected to mirror de; B3 recurs).

## Offerings sub-tab + content detail pages — German (2026-07-28)
**Evidence:** `../evidence/programs_de_offerings_tab.png`, `../evidence/programs_de_viewall_empty_modal.png`,
`../evidence/programs_de_bitecontent_detail_overlap.png`, `../evidence/programs_de_bitecontent_step2.png`.

| Test Case ID | Description | Steps | Expected | Actual (de) | Status | Priority |
|---|---|---|---|---|---|---|
| PRG-LOC-012 | Offerings sub-tab chrome localized | Click "Angebote" tab | Translated | "Kategorie"/"Unterkategorie" filters, "Körperlich"/"Mental" categories, "Workout"/"Tanz"/"Meditation" subcategories, "Partner-Angebote" heading — all ✅ | PASS | P2 |
| PRG-LOC-013 | Offerings intro line register | Read "Partner-Angebote" subtitle | Consistent informal voice | "**Um Ihre umfassenden Wellness-Bedürfnisse zu erfüllen**" uses formal *Ihre* → **B12 recurs** (3rd surface) | FAIL (de) | P2 |
| PRG-LOC-014 | Partner offer cards = BE data | Read offer titles ("assad", "heyy", "Decathalon", "Get extra 60% off on Adidas Shoes…") | Stay as authored | Correctly untranslated partner-authored names/copy → PASS (classified BE data) | PASS (classified BE data) | — |
| PRG-LOC-015 | Partner offer click → detail/redirect | Click a partner offer card | Documented behavior | Opens the **partner's external site in a new tab** (e.g. decathlon.in) — no in-app detail page exists for Offerings; not a localization surface (external site, out of scope) | PASS (behavior documented) | — |
| PRG-LOC-016 | Health-bites "Alle anzeigen" (View all) grid | Click "Alle anzeigen" | List of German bites | Modal opens ("Gesundheitstipps" title translated ✅) but **body grid is empty** — API `GET /content/category/20?page=0&perPage=12` returns `{"data":{"data":[]}}` even though the same categoryId=20 has 2 German items via the `byCategoryName` endpoint used on the main carousel → **new Bug** | FAIL (de) | P2 |
| PRG-LOC-017 | Bite-size content detail page — chrome + body | Click a Health-bites card ("Vollständiger Leitfaden für gesunde Ernährung") | Fully localized | "Einführung" heading ✅, body paragraph fully German ✅, **"Written By" label stays English** ✅❌, author name "Vantage Fit Team" stays as-is (correct, proper noun) | FAIL (de) — "Written By" | P3 |
| PRG-LOC-018 | Bite-size content body register | Read intro paragraph | Consistent informal voice | "**Ihren** Körper mit den richtigen Nährstoffen…" uses formal *Ihren* → **B12 recurs** (4th surface, now inside authored content body, not just chrome) | FAIL (de) | P2 |
| PRG-LOC-019 | Bite-size content step 1→2 navigation | Click "Fangen wir an" | Advances to step 2, translated | Advanced correctly; step 2 fully German ("Was ist gesunde Ernährung?", "Warum ist es wichtig?", body, "Nächste") ✅ | PASS | P3 |
| PRG-LOC-020 | Bite-size intro layout | Visual check of step-1 screen | CTA button doesn't collide with body text | "Fangen wir an" CTA button visually sits **mid-paragraph**, splitting the intro sentence ("...geht es" / [button] / "erhalten, das Wachstum...") inside the phone-frame preview (`.bite-device`) — **new UI bug**, needs design confirmation on intended CTA placement | FAIL (de) | P3 |

### Notes
- ~~Root cause of PRG-LOC-020 not confirmed~~ — **resolved 2026-07-28:** confirmed identical in Spanish (see
  below), ruling out a translation-length trigger; it's a language-agnostic template bug.
- ~~PRG-LOC-016's empty "View all" grid needs an English-baseline comparison~~ — **resolved 2026-07-28:**
  confirmed German-specific (see below); the same flow in Spanish returns populated content.

## Library + Offerings + content detail — Spanish (2026-07-28)
**Evidence:** `../evidence/programs_es_bitecontent.png`.

| Test Case ID | Description | Steps | Expected | Actual (es) | Status | Priority |
|---|---|---|---|---|---|---|
| PRG-LOC-021 | Sub-tabs + subtitle localized | Open Programs in Spanish | Translated | "Biblioteca"/"Ofertas" tabs, "Contenido de la biblioteca y ofertas de bienestar." — all ✅ | PASS | P2 |
| PRG-LOC-022 | Health-bites section chrome | Read section header | Translated | "Consejos rápidos", "Consejos de 15-30 seg para una vida más saludable.", "Ver todo" — all ✅ | PASS | P2 |
| PRG-LOC-023 | Content-card titles (BE/content data) | Read carousel card titles | Stay as authored per locale | "Guía completa para una alimentación saludable" — genuinely translated content ✅; but library "View all" grid also surfaces cards titled literally **"Spanish Content"** / "New SPANISH Updated English Content" — English placeholder-looking titles → content-quality observation, not an FE bug (BE/content data) | Needs Verification (content quality) | P4 |
| PRG-LOC-024 | Offerings sub-tab chrome | Click "Ofertas" tab | Translated | "Categoría"/"Subcategoría" filters, "Ofertas de socios" heading — all ✅ | PASS | P2 |
| PRG-LOC-025 | Offerings intro line register | Read "Ofertas de socios" subtitle | Consistent informal voice | "Para cuidar de **sus** necesidades de bienestar completa" — formal "sus" → **B12 recurs in Spanish**, same structural position as German's Offerings-subtitle instance | FAIL (es) — B12 | P2 |
| PRG-LOC-026 | Partner offer cards (BE data) | Read offer titles | Stay as authored | "assad", "heyy", "Decathalon", "Get extra 60%/10% off…" — unchanged ✅ [BE data] | PASS | — |
| PRG-LOC-027 | Health-bites "Ver todo" (View all) grid | Click "Ver todo" | List of Spanish bites | **Grid populated with 3 items** (unlike German's empty grid, B14) — confirms B14 is **German-specific**, not a general API bug | PASS (confirms B14 scope) | — |
| PRG-LOC-028 | Bite-size content detail — chrome + body | Click a Health-bites card | Fully localized | "Introducción" heading ✅, body paragraph fully Spanish ✅, **"Written By" label stays English** → B13 recurs identically | FAIL (es) — B13 | P3 |
| PRG-LOC-029 | Bite-size intro CTA layout | Visual check of step-1 screen | CTA doesn't collide with body text | "Empecemos" CTA button visually sits **mid-paragraph**, identical to German's B15 — confirms language-independent template bug, not a translation-length trigger | FAIL (es) — B15 confirmed | P3 |
| PRG-LOC-030 | `<html lang>` correct | Read lang attr | Matches | "es" ✅ | PASS | P3 |

### Notes (Spanish)
- Library content for Spanish includes what look like **QA/test placeholder titles** ("Spanish Content",
  "New SPANISH Updated English Content") rather than genuine health content — a content-authoring/test-data
  quality issue, flagged for the content owner, not an FE translation bug.
- No formal/informal mixing found on the Offerings partner-card grid itself, or in the bite-size content
  body paragraph checked this run (only the section subtitle carried the formal slip).

## Content-image reliability — found on second review (2026-07-28)

| Test Case ID | Description | Steps | Expected | Actual | Status | Priority |
|---|---|---|---|---|---|---|
| PRG-LOC-031 | Library thumbnails load their images | Open Library tab → inspect Health-bites carousel + Excercise cards | Real cover images load | **Nearly all thumbnails render as solid black boxes** — 28 unique image URLs 404 on page load (23 double-extensioned, 1 doubled path segment, 4 missing named assets); the fallback image itself also 404s → **new Bug B23** | FAIL — B23 | **P2** |
| PRG-LOC-032 | Offerings tab loads reliably | Open Offerings tab (repeat across multiple loads) | Grid loads every time | Observed one explicit "Unable to load offerings right now" error state, coinciding with a 502 on `/marketplace/categories`; recovered on manual "Try again" → **new Bug B24** | FAIL (intermittent) — B24 | P3 |

### Notes
- **B23 was actually visible in the very first German-pass screenshot** (`programs_de_offerings_tab.png`,
  captured 2026-07-24/07-28) but wasn't logged at the time — caught on a second, more careful visual review
  of today's evidence prompted by the user. Worth a habit change: always zoom into screenshots for visual
  defects (broken images, overlaps), not just extract text content, since text-focused review can miss
  purely visual bugs.
- B23's URLs contain no locale segment — confirmed language-independent by inspection, not re-tested in
  every language.
