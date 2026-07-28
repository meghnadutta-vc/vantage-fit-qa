# Vantage Fit Web — Programs module — Localization test cases

**Surface:** `app.vantagecircle.co.in/ng/fit/programs` (tab "Programs"). Sub-tabs: **Library / Offerings**.
**Account:** anjan.pathak@… (UAT). **Executed:** 2026-07-24 — English baseline + **German (de)**; fr/es/pt pending.
**Evidence:** `../evidence/programs_en_baseline.png`, `../evidence/programs_de.png`.

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
- Root cause of PRG-LOC-020 not confirmed (static-positioned CTA sits between two halves of one paragraph in DOM/render order inside a fixed-size mobile-frame container — could be a template issue not specific to German, needs an English-baseline comparison to confirm if length-triggered or always present).
- PRG-LOC-016's empty "View all" grid needs an English-baseline comparison of the same endpoint to confirm whether it's German-specific (content-population gap) or a general API bug independent of language.
