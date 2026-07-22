# Overview Module — Localization Test Cases

**Module:** Vantage Fit → Overview (`/fit/overview`)
**Server/tenant:** India (`dashboard-v2.vantagecircle.co.in`), company ID `355` — UAT
**Languages in scope:** English (en, baseline) · German (de) · French (fr) · Spanish (es)
**Driver:** Playwright MCP · **Access:** app → profile menu → *HR Admin Dashboard* (token handshake) → `/fit/overview`
**Executed:** 2026-07-21

> Status: PASS / FAIL / NEEDS VERIFICATION / NEEDS PRODUCT CONFIRMATION. Bugs referenced as `bug-logs/overview.md#Bug-N`.

---

## Phase 1 — Module scope & discovery (updated post-execution)

### Screens / regions on this module (fully loaded)
1. **Left icon rail** — product switcher (Recognition, Wellness/Fit, Pulse, Redemption, Milestone, Perks, Admin Hub).
2. **Fit sidebar (secondary nav)** — plan badge + *Create* button; grouped nav (Overview; Challenges; Engage→Programs/Community/Communications; Analyse→Workforce health/Reports; Manage→Rewards/Configuration).
3. **Sidebar footer** — Language switcher (18 options), Challenges counter, Licences, "Contact account manager".
4. **Top filter bar** — Country filter ("All Countries") + Date-range filter ("Last 30 Days | <range>").
5. **Top stat row** — Enrolled Users, Active Users, Incentivization, Participation Rate (each: label, "View more", value, delta "vs Prev period"/"vs Prev Quarter").
6. **Leadership Insights** card ("AI-generated").
7. **Ask Vantage Fit** — search/CTA banner ("Ask Vantage Fit anything", `⌘K`).
8. **Org Wellness Score** — score + "Score Breakdown" (Health Baselines / Participation / Activity Levels / Program Adherence).
9. **At a Glance** — activity strip (Avg Steps / Active Minutes / Mindful Minutes / Avg Sleep, "/day").
10. **Recommended Actions** — "System suggested next steps" + 10 action items (title + description).
11. **Workforce Health Snapshot** — Health Status (Normal / Needs Attention) + Top Deficiencies.
12. **Wellness Tiers** — Gold / Silver / Bronze + footnote.
13. **Active Challenges** — list + "Create new challenge".

### APIs
- `GET /assets/i18n/fit/{lang}.json` (+ en) — FE dictionaries, **991 keys, fully populated for en/de/fr/es**.
- `POST /vantagefit/api/dashboard/v1/overview/home/stream` — card data.
- `GET /vantagefit/api/v1/target/audience/fetchFilters/355` — country filter values.
- `GET dash.vantagecircle.com/ml/api/v1/vfit-chatbot/menu` — Ask Vantage Fit.

### Language control & persistence
Sidebar-footer dropdown drives whole UI. Persists in `localStorage.fit_lang`. `<html lang>` does NOT update (stays "en") — see Bug.

### FE-vs-BE classification (from i18n lookup)
- **FE (key exists, should localize):** All Countries, Last 30 Days presets, Score Breakdown, Workforce Health Snapshot, View Insights, Health Status, Top Deficiencies, Wellness Tiers (+subtitle), Active Challenges, View all, View More, Overview/Challenges/Licenses (sidebar — already working).
- **Not in i18n (hardcoded literal OR backend/data):** Org Wellness Score, stat-card labels (Enrolled/Active Users, Incentivization, Participation Rate), "Across all countries and demographics", At a Glance + activity labels, Recommended Actions + all items, Health Baselines/Activity Levels/Program Adherence, "Aggregated insights only", Normal/Needs Attention, "Based on avg daily steps over 21 days".
- **Backend/data (expected EN until backend phase):** challenge status ("Active"), challengeTypeName ("Multi Week Multi Activity"), "Ended on <date>", deficiency names (Vitamin D / Sleep Quality / Stress Levels), health-status values.

---

## Phase 2 — Test cases (executed)

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| OVW-TC-001 | Language switcher options localized | On `/fit/overview` | Open language dropdown | Option labels consistent per language | Options shown as fixed English endonyms in all languages (English, Arabic, German…). Consistent, arguably by design (endonym list). | NEEDS PRODUCT CONFIRMATION | P3 |
| OVW-TC-002 | German localizes entire sidebar nav | en baseline captured | Select German; read all sidebar labels | All sidebar labels German | Sidebar fully German (Übersicht, Challenges, Berichte, Konfiguration…). PASS. | PASS | P2 |
| OVW-TC-003 | French localizes entire sidebar nav | en baseline | Select French; read sidebar | All French | Sidebar fully French (Aperçu, Défis, Rapports…). PASS. | PASS | P2 |
| OVW-TC-004 | Spanish localizes entire sidebar nav | en baseline | Select Spanish; read sidebar | All Spanish | Sidebar fully Spanish (Resumen, Desafíos, Informes…). PASS. | PASS | P2 |
| OVW-TC-005 | Main card titles localize (de/fr/es) | On Overview | Read "Org Wellness Score", "Workforce Health Snapshot", "Wellness Tiers", "Active Challenges" | All localized | ALL FOUR remain English in de/fr/es though `overview.*` keys exist (except "Org Wellness Score" which has no key). See Bug #1. | FAIL | P2 |
| OVW-TC-006 | No mixed-language within screen | de, fr, es | Full-page capture; enumerate strings | No EN/target mix | Heavy mix: sidebar + a few CTAs localized, entire main content English. See Bug #1. | FAIL | P2 |
| OVW-TC-007 | Card CTAs consistent | per language | Inspect each card action link | Same language/wording | "View More" (Org Wellness) English while other cards localize ("Ver más"/"Mehr anzeigen"/"Voir plus"); "View all" English. See Bug #3. | FAIL | P2 |
| OVW-TC-008 | Score-breakdown labels localized | Org Wellness Score card | Read "Score Breakdown" + 4 rows | Localized | "SCORE BREAKDOWN" English (key exists → Bug #1); 4 row labels English (no key → likely backend/hardcoded, Bug #1 note). | FAIL | P2 |
| OVW-TC-009 | Workforce Health Snapshot labels localized | On Overview | Read snapshot labels | Chrome localized | "Workforce Health Snapshot", "View Insights", "Health Status", "Top Deficiencies" all English despite keys existing. See Bug #1. | FAIL | P2 |
| OVW-TC-010 | Wellness Tiers labels localized | On Overview | Read title/subtitle/tiers/footnote | Localized | Title + subtitle English despite keys; Gold/Silver/Bronze English; footnote English. See Bug #1. | FAIL | P3 |
| OVW-TC-011 | Country filter default localized | On Overview | Read country filter default | "All Countries" localized | Stays "All Countries" in all 3 langs (key `targetAudience.filtersAll.country` exists). See Bug #2. | FAIL | P2 |
| OVW-TC-012 | Date-range preset labels localized | On Overview | Read date preset label | Localized | Inconsistent: "Últimos 30 días" in es, but "Last 30 Days" stays English in de/fr (keys exist for both). Second "Last 30 Days" (At a Glance) English in all. See Bug #7. | FAIL | P2 |
| OVW-TC-013 | Date-range VALUE locale-formatted | On Overview | Read "Jun 21, 2026 - Jul 20, 2026" | Locale format | Stays US format `Jun 21, 2026 - Jul 20, 2026` in all languages. See Bug #5. | FAIL | P2 |
| OVW-TC-014 | Number / percentage / currency locale-formatted | On Overview | Read 23.7%, 33.2%, $0 | Locale format | Dot decimals `23.7%` and `$0` (USD) in de/fr/es (expect comma decimal, localized currency). Some values from stream API — see Bug #6 (needs FE/BE split). | NEEDS VERIFICATION | P3 |
| OVW-TC-015 | "Ask Vantage Fit" banner localized | On Overview | Read banner + example prompt + ⌘K | Localized | Banner reachable via ⌘K; not fully re-verified per language this pass. | NEEDS VERIFICATION | P3 |
| OVW-TC-016 | Active Challenges card chrome localized | On Overview | Read title, "View all", "Create new challenge" | Chrome localized | "Active Challenges" + "View all" English; "Create new challenge" DID localize (Crear Nuevo Reto / Neue Challenge Erstellen / Créer Un Nouveau Défi). Partial — see Bug #1/#3. | FAIL | P2 |
| OVW-TC-017 | Challenge status/type — FE vs BE | On Overview | Inspect "Active", "Multi Week Multi Activity", "Ended on…" | Classify | Not in i18n → BACKEND/data. Expected English until backend localization phase. Documented, not a FE bug. | PASS (backend-deferred) | P2 |
| OVW-TC-018 | Plan badge & Create button | On Overview | Read plan badge + Create button | Localized | Sidebar "Create/Crear/Créer/Erstellen" localized; plan name "Grow" is product data. | NEEDS PRODUCT CONFIRMATION | P3 |
| OVW-TC-019 | Sidebar footer stats localized | On Overview | Read Challenges/Licences/Contact labels | Localized | Localized (Licencias/Lizenzen/Licences etc.). PASS. | PASS | P3 |
| OVW-TC-020 | German long-string truncation/overlap | de | Full-page screenshot; inspect | No clipping/overlap | No truncation/overlap observed (evidence/overview_de.png); most main content still English so German expansion largely untested on cards. | PASS (partial) | P3 |
| OVW-TC-021 | Loading state localized | On Overview | Reload; observe skeleton | Localized | Skeleton is non-textual; first Spanish load rendered partial cards (data streaming) — no untranslated loading text seen. | PASS | P4 |
| OVW-TC-022 | Empty state localized | On Overview | Observe zero-data cards (0% challenges) | Localized | Zero values shown (0, 0%, $0); no distinct empty-state copy triggered. | NEEDS VERIFICATION | P4 |
| OVW-TC-023 | Tooltips on score-breakdown items localized | On Overview | Hover/click breakdown rows | Localized | Not verified this pass (rows are clickable; tooltip content not captured). | NEEDS VERIFICATION | P3 |
| OVW-TC-024 | Accessibility labels localized | On Overview | Inspect aria-labels on icon buttons | Localized | aria-labels observed in English (e.g. "Collapse sidebar", "Open profile menu") regardless of language. See Bug #4 note. | FAIL | P3 |
| OVW-TC-025 | Language persists across reload & nav | de | Reload; navigate away & back | Persists | `localStorage.fit_lang` set correctly per selection; persists. PASS. | PASS | P2 |
| OVW-TC-026 | `<html lang>` reflects selected language | On Overview | Read `document.documentElement.lang` per lang | Matches selection | Stuck at `"en"` for de/fr/es. See Bug #4. | FAIL | P3 |
| OVW-TC-027 | Top stat-row labels localized | On Overview | Read Enrolled/Active Users, Incentivization, Participation Rate | Localized | All English in de/fr/es (no i18n key found → hardcoded/backend). See Bug #1. | FAIL | P2 |
| OVW-TC-028 | "Recommended Actions" section localized | On Overview | Read title + 10 action items | Localized | Entire section English in de/fr/es (title, "System suggested next steps", all 10 items + descriptions). See Bug #1. | FAIL | P2 |
| OVW-TC-029 | "At a Glance" activity strip localized | On Overview | Read title + Avg Steps/Active/Mindful Minutes/Avg Sleep + "/day" | Localized | Entirely English in de/fr/es. See Bug #1. | FAIL | P2 |
| OVW-TC-030 | Delta labels consistent ("vs Prev period" / "vs Prev Quarter") | On Overview | Read deltas on stat + score cards | Consistent & localized | "vs Prev Quarter" localizes (vs. Vorquartal / vs trimestre précédent / vs. trimestre anterior) but "vs Prev period" stays English → inconsistent. See Bug #3. | FAIL | P3 |
