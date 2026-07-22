# Programs → Content Library Module — Localization Test Cases

**Module:** Vantage Fit → Programs → Content Library (`/fit/programs/on-demand-content`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company `355` — UAT
**Languages:** English (baseline) · German (deep)
**Executed:** 2026-07-21 · Evidence: `evidence/contentlibrary_*`

> **Scope note:** The Programs group also has *Create Content* (`?action=create`, the Bite-Size
> builder covered separately under VF-2126). This pass covers the **Content Library listing** page.
>
> **Methodology:** verified on FRESH route loads per language. FE-vs-BE classified against the
> app i18n dict `/assets/i18n/fit/{de,en}.json` (991 keys). No content was created/edited/deleted
> (read-only pass) — table rows are pre-existing tenant data (many junk QA rows, backend-owned).

---

## Phase 1 — Scope & discovery

### Regions
1. **Header:** heading "Inhaltsbibliothek" + subtitle "Verwalten Sie die Verfügbarkeit von Inhalten und kuratieren Sie hervorgehobene Elemente." + "Erstellen" button (also a duplicate top-right "Erstellen").
2. **Toolbar:** search box (placeholder "Inhalte suchen..."), **Type filter** dropdown, **Category filter** dropdown.
3. **Table:** columns "Inhalt" / "Typ" / "Kategorie" / "Aktionen"; rows = content items (title + tagline, type badge, category, actions). Actions: "Inhalt ansehen" link (opens external URL) + an icon button (edit/menu). Bite-Size rows show a "N language(s)" badge in the Type cell and only the icon button (no external link).
4. **Content Overview panel** (right): "Inhaltsübersicht" with counts — "12 Artikel", "4 Videos", "10 Podcasts", "13 Bite Size".

### i18n classification (dict lookup — authoritative)
- **Keys exist & translated (should localize):** `contentLibrary.types.all`="Alle", `.article`="Artikel", `.video`="Video", `.podcast`="Podcast", `.bite_content`="Bite Size"; `contentLibrary.stats.articles`="Artikel"; `contentLibrary.table.type`="Typ"; `common.filter`="Filtern".
- **Backend/content data (expected as-entered):** row titles/taglines; **category values** (Mindfuless[sic], Excercise[sic], Healthy Eating, Yoga Is Good, Meditation, Quit Smoking in 30 Days, Health Bites) — data typos are content-entry issues, not localization.
- **"Bite Size" type label** kept English in both langs by design (brand term) → correct.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| CL-TC-001 | Page heading + subtitle localized | On `/fit/programs/on-demand-content`, fresh load per lang | Read header block | Localized | de "Inhaltsbibliothek" / "Verwalten Sie die Verfügbarkeit…"; en equivalents. PASS. | PASS | P2 |
| CL-TC-002 | "Create" / "Erstellen" buttons localized | de fresh load | Read both create buttons | Localized | "Erstellen" (both). PASS. | PASS | P3 |
| CL-TC-003 | Search placeholder localized | de fresh load | Read search box | Localized | "Inhalte suchen...". PASS. | PASS | P3 |
| CL-TC-004 | Table column headers localized | de fresh load | Read headers | Localized | "Inhalt" / "Typ" / "Kategorie" / "Aktionen". PASS. | PASS | P2 |
| CL-TC-005 | "View content" action link localized | de fresh load | Read row action link | Localized | "Inhalt ansehen". PASS. | PASS | P3 |
| CL-TC-006 | Content Overview panel localized | de fresh load | Read summary counts | Localized | "Inhaltsübersicht"; "Artikel / Videos / Podcasts / Bite Size". PASS. | PASS | P2 |
| CL-TC-007 | **Type filter options localized** | de; open Type dropdown | Read options | "Alle / Artikel / Video / Podcast / Bite Size" | Options render **English**: "All / Article / Video / Podcast / Bite Size" though `contentLibrary.types.*`=Alle/Artikel exist. See **CL#1**. | FAIL | P2 |
| CL-TC-008 | **Table "Typ" column values localized** | de fresh load | Read Type cells | Localized (Artikel, Video, Podcast, Bite Size) | Article-type rows show **"Article"** (English), not "Artikel" — inconsistent with the summary panel's "Artikel". See **CL#1**. | FAIL | P2 |
| CL-TC-009 | **Category filter trigger localized** | de; inspect Category filter | Read button label vs selected option | Button reflects localized selection | Button shows **"All"** while the selected dropdown option is **"Alle"** — trigger label not localized. See **CL#2**. | FAIL | P3 |
| CL-TC-010 | Category filter options localized | de; open Category dropdown | Read options | "Alle" + category data | "Alle" localized ✓; rest are backend category names (Mindfuless[sic] etc.) — data, expected. PASS. | PASS | P3 |
| CL-TC-011 | **Bite-Size "N language(s)" badge localized** | de fresh load | Read Bite-Size type cells | "1 Sprache" / "N Sprachen" | Renders **English** "1 language" / "2 languages" / "7 languages"; no matching plural i18n key found → hardcoded FE literal. See **CL#3**. | FAIL | P3 |
| CL-TC-012 | Ask-Vantage-Fit assistant widget localized | de fresh load | Read floating widget + suggestion | Localized | English: "Ask Vantage Fit anything" + rotating EN prompts. Global element across Fit pages. See **CL#4** (observation/cross-module). | FAIL (cross-module) | P4 |
| CL-TC-013 | Action-column icon buttons labeled | de fresh load | Inspect icon buttons | Accessible name present | Icon buttons expose empty name (button ""). Minor a11y. See **CL#5**. Not a localization defect. | FAIL (a11y) | P4 |
| CL-TC-014 | English baseline clean (no German leak) | en fresh load | Read whole page | English throughout | All strings English incl. type filter/table ("All/Article"), summary ("Articles"). Clean. PASS. Evidence: contentlibrary_en_baseline.png | PASS | P2 |
| CL-TC-015 | Search behaviour + result copy localized | de; type a query | Search + read empty/result state | Localized empty state | NOT EXECUTED (no-typing this pass to avoid altering table state view); empty-filter key exists (`reports.emptyFiltered`="Keine Daten…"). Needs verification. | NEEDS VERIFICATION | P3 |
| CL-TC-016 | Type/Category filter actually filters + counts update | de; apply a filter | Select "Artikel"/a category | Table + overview update | NOT EXECUTED (functional, out of localization scope this pass). Needs verification. | NEEDS VERIFICATION | P3 |
| CL-TC-017 | Row action-menu (icon button) contents localized | de; open row menu | Click icon button | Localized menu/dialog | NOT EXECUTED — read-only pass, avoided edit/delete actions on tenant data. Needs verification. | NEEDS VERIFICATION | P3 |
| CL-TC-018 | Pagination / lazy-load controls localized | de fresh load | Look for paginator | Localized or N/A | No paginator observed; full list rendered (39 rows). N/A this view. | N/A | P4 |
| CL-TC-019 | `<html lang>` reflects selected language | de active | Inspect document lang | `de` | Cross-module carry-over (Overview #4) — same app shell; not re-counted. | FAIL (cross-module) | P3 |
| CL-TC-020 | French / Spanish deep pass | fr/es fresh load | Repeat CL-TC-007..011 | Localized | NOT EXECUTED this pass (de deep + en baseline). Needs verification. | NEEDS VERIFICATION | P3 |

---

## Phase 4 — Summary

- **Localized correctly:** header, subtitle, create buttons, search placeholder, table headers, "Inhalt
  ansehen", Category filter **options** ("Alle" + data), and the Content Overview panel ("Artikel/Videos/
  Podcasts/Bite Size").
- **Module bugs:** **CL#1 (P2)** content-type labels English in Type filter + table "Typ" column (dict has
  Alle/Artikel; summary panel proves it's wired elsewhere → FE wire-up gap, mixed-language);
  **CL#2 (P3)** Category filter trigger button "All" while options localize to "Alle";
  **CL#3 (P3)** Bite-Size "N language(s)" badge hardcoded English.
- **Observations:** CL#4 (P4) Ask-Vantage-Fit assistant widget English (global/cross-module);
  CL#5 (P4 a11y) action icon buttons unlabeled. Cross-module `<html lang>` (Overview #4).
- **Not executed:** search/empty-state copy, filter-apply functional + count refresh, row action-menu
  (read-only pass, avoided mutating tenant data), fr/es deep pass, non-India servers.
