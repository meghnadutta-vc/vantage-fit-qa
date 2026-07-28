# Vantage Fit Web — Localization Bug Report (consolidated)

**Surface:** Employee Vantage Fit web — `app.vantagecircle.co.in/ng/fit/summary` (heart icon)
**Account / tenant:** `anjan.pathak@… (UAT test account)` — UAT
**Languages tested:** German (de), French (fr), Spanish (es), Portuguese (pt) + English baseline
**Date:** 2026-07-24 · **Module covered so far:** Summary
**How to reproduce any bug below:** Profile avatar → View Profile → My Info → **Edit Profile → Language → Save** (forces logout → re-login via `api.vantagecircle.co.in`) → open `/ng/fit/summary`.
**Screenshots:** `../evidence/summary_de.png`, `summary_fr.png`, `summary_es.png`, `summary_pt.png`, baseline `fit_summary_landing.png`.

---

## 🔧 Assign to FRONTEND developer (top priority first)

| # | Priority | Bug title |
|---|---|---|
| B1 | **P2** | Dates show in English format/text in every language |
| B2 | **P2** | Language-change alert prints a literal `{language}` placeholder (de/fr/es) |
| B3 | **P2** | "Challenges" navigation tab not translated in German |
| B4 | **P2** | "Week 1" (challenge week label) not translated in any language |
| B5 | **P2** | Highlights "Posted by / Likes / Comments / 2 days ago" not translated |
| B6 | P3 | Measurement units (mins / sec / hrs / /day) stay English |
| B7 | P3 | Trend-chart weekday axis "S M T W T F" not localized |
| B8 | P3 | "Active Minutes" label capitalized inconsistently (fr & pt) |
| B9 | P4 | "Wellness Score" stays English (confirm if intentional brand term) |
| B10 | P4 | i18n JSON asset requests return the SPA HTML shell (infra) |
| B11 | **P2** | Language preference not persisted — reverts to English after session expiry/re-login (FE/BE TBD) |
| B12 | **P2** | German mixes formal "Ihr" and informal "du" — inconsistent tone/register (cross-module; 3 surfaces) |
| B13 | P3 | "Written By" label not translated in bite-size content detail (Programs) |
| B15 | P3 | CTA button overlaps body text in bite-size content intro screen (Programs) — root cause TBD |
| B16 | **P2** | Community module chrome 0% localized; nav/footer regress to English while on this route |
| B17 | **P2** | "You are currently in a caloric deficit" sentence not translated (Diary) |
| B18 | P3 | "mile" unit word not translated in Diary's Distance section |
| B19 | **P2** | Trends (`/activity-stats`) page mostly unlocalized; inconsistent even within itself |

## 🗄️ Assign to BACKEND developer

**None identified in the Summary module.** All backend/data-driven strings behaved correctly across
de/fr/es/pt (they stayed as authored, which is expected): the challenge name "QA-BOT Custom 0721", the
Highlights post title "Q3 Wellness Program — Now Live", and the user name "Anjan Pathak". No server-rendered
string was found mis-localized here. (Re-evaluate per module — backend candidates may appear in Challenges/
Reports-type screens.)

**Programs (2026-07-28):** **B14** — "Alle anzeigen" content grid returns empty (`GET /content/category/20`)
while the same category has content via a sibling endpoint (`POST /content/byCategoryName`) — likely a
locale-handling gap on the paginated endpoint. P2, FE/BE TBD pending an English-baseline comparison.

---

# Detailed bugs (top priority first)

## B1 — [P2] Dates display in English format/text in every language
**Type:** Localization / Copy · **Layer:** Frontend
**Where:** Summary — header date, badge date, trend ranges, "Updated on" dates.

**Description & proof:** In de/fr/es/pt the "Updated on…" *prefix* is translated but the date itself is not,
and the main dates stay fully English. Captured on fresh loads:
- Header: **"Friday, 24 July 2026"** — identical in all 4 languages (should be e.g. de "Freitag, 24. Juli 2026").
- Badge: **"24th Jul 2026"** (English ordinal + month) in all 4.
- Trend ranges: **"18 - 24 Jul"**, **"17 - 24 Jul"** in all 4.
- de "Aktualisiert am **14 Jul 2025**", fr "Mis à jour le **14 Jul 2025**", es "Actualizado el **14 Jul 2025**",
  pt "Atualizado em **14 Jul 2025**" — prefix localized, date English.

**Expected:** locale-formatted dates with translated weekday/month names.
**Screenshot (crop):** `../evidence/bug_B1_date_header_pt.png` · full page `../evidence/summary_de.png`.

![B1 — English date header (crop)](../evidence/bug_B1_date_header_pt.png)

---

## B2 — [P2] Language-change alert prints literal `{language}` placeholder
**Type:** Localization / Copy · **Layer:** Frontend
**Where:** My Profile → Edit Profile → change Language → Save (confirmation alert).

**Description & proof:** The confirmation alert's translated strings contain an un-interpolated `{language}`
token. Exact captured alert text:
- **de:** "Sie haben Ihre Sprache in **{language}** geändert. Bitte melden Sie sich erneut an, um auf die Website zuzugreifen."
- **fr:** "Vous avez changé votre langue pour **{language}**. Veuillez vous reconnecter pour accéder au site."
- **es:** "Has cambiado tu idioma a **{language}**. Inicia sesión de nuevo para acceder al sitio."
- **pt:** "Você alterou seu idioma para **{language}**. Faça login novamente para acessar o site."
- **en (correct):** "You have changed your language to **German**. Please login again to access the site."
- → confirmed broken in **all four** non-English locales (de/fr/es/pt); only English interpolates.

**Expected:** the chosen language name interpolated, as English already does.
**Root cause (likely):** the translated strings use a placeholder token the interpolation doesn't replace.
**Screenshot:** none — this is a native browser alert (text captured verbatim above as proof).

---

## B3 — [P2] "Challenges" tab not translated in German
**Type:** Localization · **Layer:** Frontend
**Where:** Summary — top navigation tabs.

**Description & proof:** The 4 tabs translate in fr/es/pt but German leaves "Challenges" in English:
| Tab | de | fr | es | pt |
|---|---|---|---|---|
| Challenges | **Challenges** ❌ | Défis ✅ | Retos ✅ | Desafios ✅ |

(Summary/Programs/Community translate fine in all 4.) → German dictionary is missing this tab's entry.
**Expected:** de "Herausforderungen" (or agreed term).
**Screenshot:** `../evidence/summary_de.png` (tab bar shows "Challenges") vs `../evidence/summary_fr.png` ("Défis").

![B3 German tabs — "Challenges" untranslated](../evidence/summary_de.png)

---

## B4 — [P2] "Week 1" not translated in any language
**Type:** Localization · **Layer:** Frontend
**Where:** Summary → Challenges card (week badge).

**Description & proof:** Renders literal **"Week 1"** in de/fr/es/pt (captured in all four string dumps).
**Expected:** de "Woche 1", fr "Semaine 1", es/pt "Semana 1".
**Screenshot (crop):** `../evidence/bug_B4_week1_pt.png` (Challenges card — badge still reads "Week 1" in Portuguese).

![B4 — "Week 1" untranslated (crop, pt)](../evidence/bug_B4_week1_pt.png)

---

## B5 — [P2] Highlights social strings not translated
**Type:** Localization · **Layer:** Frontend
**Where:** Summary → Highlights (community post) card.

**Description & proof:** In all 4 languages the card shows English: **"Posted by"**, **"0 Likes"**,
**"0 Comments"**, and relative time **"2 days ago"**. (The post *title* "Q3 Wellness Program — Now Live" is
user/backend data and correctly stays as authored — not a bug.)
**Expected:** translated labels + localized relative time (e.g. de "vor 2 Tagen", "Gepostet von", "Kommentare").
**Screenshot (crop):** `../evidence/bug_B5_highlights_social_pt.png` (post shows "2 days ago / Posted by / Likes / Comments" in English while in Portuguese).

![B5 — Highlights social strings English (crop, pt)](../evidence/bug_B5_highlights_social_pt.png)

---

## B6 — [P3] Measurement units stay English
**Type:** Localization · **Layer:** Frontend
**Where:** Summary → Snapshot & Trends cards.

**Description & proof:** Unit abbreviations unchanged in all 4: **"6 hrs 51 mins"**, **"7 mins"**, **"0 sec"**,
**"2857 /day"**, **"983/32 mins"**. Units appear concatenated as hardcoded English.
**Expected:** locale-appropriate units.
**Screenshot (crop):** `../evidence/bug_B6_B7_units_axis_pt.png` (Trends card in Portuguese — "6 hrs 51 mins", "/day", and the "S M T W T F" axis all English). Same crop evidences **B7**.

![B6/B7 — units & weekday axis English (crop, pt)](../evidence/bug_B6_B7_units_axis_pt.png)

---

## B7 — [P3] Trend-chart weekday axis not localized
**Type:** Localization · **Layer:** Frontend
**Where:** Summary → Trends charts (x-axis).

**Description & proof:** Axis initials render **"S M T W T F"** (English) in all 4 languages.
**Expected:** localized weekday initials (e.g. de "M D M D F S S").
**Screenshot (crop):** `../evidence/bug_B6_B7_units_axis_pt.png` (weekday letters "S M T W T F" under the Trends chart).

![B7 — weekday axis English (crop, pt)](../evidence/bug_B6_B7_units_axis_pt.png)

---

## B8 — [P3] "Active Minutes" capitalized inconsistently (fr & pt)
**Type:** Localization / UI consistency · **Layer:** Frontend
**Where:** Summary — Snapshot card vs Trends card (same metric).

**Description & proof:** The same label uses different casing in two places:
- fr: **"Minutes Actives"** (Snapshot) vs **"Minutes actives"** (Trends)
- pt: **"Minutos Ativos"** (Snapshot) vs **"Minutos ativos"** (Trends)
**Expected:** one consistent capitalization.
**Screenshot:** `../evidence/summary_fr.png` (compare Snapshot vs Trends card labels).

![B8 French casing inconsistency](../evidence/summary_fr.png)

---

## B9 — [P4] "Wellness Score" stays English (judgment call)
**Type:** Localization / Copy · **Layer:** Frontend
**Where:** Summary → Health card.

**Description & proof:** "Wellness Score" is unchanged in all 4 languages. May be an intentional brand/product
term (like "Vantage Fit"). **Needs product confirmation** whether it should localize.
**Screenshot:** `../evidence/summary_es.png` (Health card: "Wellness Score 60.58").

---

## B10 — [P4] i18n JSON asset requests return the SPA HTML shell
**Type:** Infrastructure · **Layer:** Frontend / build config
**Where:** network — `/ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json`.

**Description & proof:** The app requests those 7 locale files, but each responds with
`content-type: text/html` (the SPA `index.html`), i.e. `<!DOCTYPE html> <html lang="en" …`, not JSON.
Translations still render (strings are served/bundled elsewhere), so it is **not user-visible** — but the
requests are dead/HTML-fallback and suggest a misconfigured asset path worth cleaning up.
**Proof:** fetch of `/ng/assets/i18n/fit/en.json` → 200, `content-type: text/html`, body begins
`"<!DOCTYPE html> <html lang=\"en\" data-beasties-container>"`.
**Screenshot:** n/a (network/console evidence; recorded in `Execution_Status.md`).

---

## B11 — [P2] Language preference not persisted across sessions
**Type:** Localization / Functional · **Layer:** Frontend / Backend (TBD)
**Where:** Profile language ↔ Fit web, after a session expiry + re-login.

**Description & proof:** A saved language reverts to English on the next login.
- Saved profile Language = German earlier; Fit web rendered German that session (verified, `challenges_de.png`).
- After the browser session expired and I re-logged in, the Fit web loaded in **English** (`html lang="en"`,
  `programs_en_baseline.png`), and the profile "Edit Profile → Language" select read back **"English"**.
- Re-selecting German + re-login restored German for that session only.

**Expected:** a saved language preference persists across logout/login until changed.
**Note/Doubt:** could be (a) language stored session-only, not persisted to the account, or (b) default-to-
  English on session bootstrap. Reproduced once via natural session expiry. Needs dev confirmation of
  intended persistence and whether it is FE (session/local) or BE (account preference). Discovered during
  the Programs pass. [FE/BE — TBD]
**Screenshot:** `../evidence/programs_en_baseline.png` (loaded EN after re-login).

---

## B12 — [P2] German mixes formal "Ihr" and informal "du" (inconsistent tone/register)
**Type:** Localization / Copy (tone consistency) · **Layer:** Frontend
**Where:** Cross-module — Summary, Programs (Offerings sub-tab + bite-size content body) vs the rest of the
German UI.

**Description & proof:** German has two politeness registers (formal *Sie/Ihr* vs informal *du/dein*). The
Fit web mixes them, which reads as inconsistent voice.
- **Informal (du)** dominates: "**Sieh**, was in **deiner** Community passiert" (Summary), "Scanne, um
  **dich** auf **deinem** Smartphone anzumelden" (footer), "Brauchst **du** Hilfe mit Vantage Fit?" (footer),
  "**Tritt** gegen Kollegen an und **verfolge deine** Aufgaben." (Challenges).
- **Formal (Ihr/Ihre/Ihren)** appears in 3 confirmed surfaces:
  1. "**Ihr** neuestes Abzeichen" (Summary — Sie-register possessive).
  2. "Um **Ihre** umfassenden Wellness-Bedürfnisse zu erfüllen" (Programs → Offerings sub-tab subtitle).
  3. "**Ihren** Körper mit den richtigen Nährstoffen…" (Programs → bite-size content intro **body copy**,
     not just UI chrome — shows the register split reaches authored content text too).
→ Summary alone already contains both registers ("Ihr neuestes Abzeichen" formal + "deiner Community"
informal); Programs adds two more formal instances, one of them inside content body text.

**Expected:** a single, consistent register across the product (Vantage Fit generally uses informal *du*).
**Fix:** change "Ihr neuestes Abzeichen" → "Dein neuestes Abzeichen", "Ihre umfassenden" → "Deine
umfassenden", "Ihren Körper" → "Deinen Körper" (and audit all screens/content templates for stray Sie/Ihr).
**Screenshot:** `../evidence/summary_de.png`, `../evidence/programs_de_offerings_tab.png`,
`../evidence/programs_de_bitecontent_detail_overlap.png`.

---

## B13 — [P3] "Written By" label not translated in bite-size content detail
**Type:** Localization · **Layer:** Frontend (likely; unconfirmed — see note)
**Where:** Programs → Health-bites → content detail dialog (byline).

**Description & proof:** The bite-size content detail dialog is otherwise fully localized into German
("Einführung" heading, full body paragraph, "Fangen wir an" CTA), but the author byline label stays English.

**Expected:** "Written By" renders in German (e.g. "Geschrieben von").
**Actual:** label shows "Written By"; the value "Vantage Fit Team" correctly stays as-is (proper noun/brand).
**Note/Doubt:** could not classify via the i18n dictionary — `/assets/i18n/fit/de.json` returns the SPA HTML
shell (see B10), blocking key-lookup. Given every other string on this exact dialog translates, a hardcoded
FE string is the likely explanation, but not confirmed via bundle search. [FE — likely, TBD]
**Screenshot:** `../evidence/programs_de_bitecontent_detail_overlap.png`.

---

## B14 — [P2] Health-bites "Alle anzeigen" (View all) opens an empty content grid
**Type:** Functional / Backend · **Layer:** Backend (likely; unconfirmed — see note)
**Where:** Programs → Health-bites → "Alle anzeigen" modal.

**Description & proof:** Clicking "Alle anzeigen" opens a modal titled "Gesundheitstipps" (title correctly
translated) but the grid inside is empty — even though German content for this exact category demonstrably
exists and renders elsewhere on the same page.
- `GET /content/category/20?page=0&perPage=12` (fired by the modal) → 200 OK, body `{"data":{"data":[]}}`.
- `POST /content/byCategoryName` (fired by the homepage carousel, same `categoryId: 20`) → 200 OK, body
  contains 2 German items: "Vollständiger Leitfaden für gesunde Ernährung" and "Schlaf verstehen: Warum
  Schlaf für die allgemeine Gesundheit wichtig ist".

**Expected:** the modal lists the same German health-bites content the carousel shows.
**Actual:** modal grid renders empty; the paginated endpoint doesn't return content the other endpoint does.
**Note/Doubt:** not yet confirmed whether this reproduces in English (→ general API bug, not localization) or
is German-specific (→ a locale param mishandled on the paginated endpoint specifically). Needs an
English-baseline comparison and dev confirmation. [BE — likely, TBD]
**Screenshot:** `../evidence/programs_de_viewall_empty_modal.png`.

---

## B15 — [P3] CTA button overlaps body text in bite-size content intro screen
**Type:** UI · **Layer:** Frontend (unconfirmed — see note)
**Where:** Programs → Health-bites → bite-size content detail, step 1 ("Einführung").

**Description & proof:** The "Fangen wir an" CTA button renders visually in the middle of the intro
paragraph inside the phone-frame preview (`.bite-device` container), splitting one sentence into two halves
above and below the button.

**Expected:** CTA sits below/after the body text with clear separation, not interrupting it.
**Actual:** button (`position: static`, no transform/negative margin) sits between two fragments of the same
paragraph in render order; the container doesn't scroll-overflow (scrollHeight == clientHeight), so this
isn't the classic "translated text is longer than English" overflow.
**Note/Doubt:** root cause not confirmed — could not isolate why the visual position interleaves with the
text via DOM/computed-style inspection. Needs an English-baseline comparison to rule out a language-agnostic
template bug vs. a German-text-length trigger. Flagging as UI pending that confirmation, not as a
localization defect. [Layer TBD]
**Screenshot:** `../evidence/programs_de_bitecontent_detail_overlap.png`.

---

## B16 — [P2] Community module chrome not localized (0% coverage); nav/footer regress to English on this route
**Type:** Localization · **Layer:** Frontend
**Where:** Community — both Social and Events sub-tabs, plus the shared app nav/footer while on this route.

**Description & proof:** Every Community-owned string renders in English regardless of the account's German
setting, on both sub-tabs — and the shared nav/footer (which correctly render German on Summary/Programs in
the same session) also regress to English specifically while on this route.
- Social tab (EN): "Community" heading, "What your wellness community is up to." subtitle, "Social"/"Events"
  tab labels, "FROM LEADERSHIP", "A note from CEO", "CHIEF EXECUTIVE OFFICER".
- Events tab (EN): "Event Calendar", weekday abbreviations "MON TUE WED THU FRI SAT SUN", "Upcoming events",
  "No upcoming events scheduled."
- Nav/footer (EN, only on this route): "Summary/Challenges/Programs/Community" tabs, "Scan to sign in on your
  phone", "Sweat now, Shine later.", "© 2026 Vantage Fit. Built for healthier teams.", "Need Help with
  Vantage Fit?" — reloading Summary/Programs immediately after in the same session shows these correctly in
  German, ruling out a session-wide language revert.
- The only German strings on the page are borrowed from already-localized shared components: "Es gibt
  keinen Beitrag" (empty state), the challenge widget ("Wöchentlicher Rang/Fortschritt"), and the badge
  widget ("Ihr neuestes Abzeichen" — carries B12).

**Expected:** Community chrome localizes like the other three modules; nav/footer stay German everywhere.
**Note/Doubt:** root cause narrowed (not fully confirmed) via the cross-module consistency pass — **this is
NOT a session-wide language revert.** If the account's language had actually reverted, the reused shared
components (empty-state text, challenge widget, badge widget) would also render English, since they pull
from the same i18n context as everything else — instead they stay correctly German. The puzzling part is
that the app-shell nav/footer (which live OUTSIDE Community's own component tree) also flip to English while
mounted here, then flip back to German on Summary/Programs. That rules out "Community was simply never wired
to i18n" as a complete explanation (a module that just doesn't consume translations wouldn't be able to
affect its siblings) — it points instead to something in Community's mount/bootstrap **resetting or
overriding a shared language-state service** that nav/footer also read from, reverting it to an English
default for as long as Community stays mounted. The same "one correctly-German string stranded in an
all-English view" signature appears on Trends (B19 — "Dieser Monat"), suggesting a related mechanism, but
Trends does NOT drag the nav/footer down with it, so the two aren't identical. Needs dev confirmation,
ideally by inspecting whether Community's module init touches a global locale/language store. [FE]
**Screenshot:** `../evidence/community_de_social_tab.png`, `../evidence/community_de_events_tab.png`.

---

## B17 — [P2] "You are currently in a caloric deficit" sentence not translated
**Type:** Localization / Copy · **Layer:** Frontend
**Where:** Diary (`/ng/fit/summary/diary`) → Calorie Balance card.

**Description & proof:** The Calorie Balance card is otherwise fully German ("Kalorienbilanz", "Empfohlen",
"Mahlzeiten/Ruhe/Aktiv/Bilanz/Defizit"), and the adjacent "Mehr erfahren" link translates correctly, but the
status sentence itself stays English: "You are currently in a caloric deficit".

**Expected:** the sentence renders in German (e.g. "Du befindest dich derzeit in einem Kaloriendefizit").
**Note/Doubt:** only the deficit-state wording was observed; the surplus-state phrasing wasn't tested. [FE]
**Screenshot:** `../evidence/diary_de_full.png`.

---

## B18 — [P3] "mile" unit word not translated in Diary's Distance section
**Type:** Localization · **Layer:** Frontend
**Where:** Diary → Distance ("Distanz") section.

**Description & proof:** Section labels ("Distanz", "Zurückgelegt", "Joggen / Laufen", "Radfahren") all
translate, but the unit word itself stays the English "mile" (e.g. "3.47 mile") instead of "Meile".

**Expected:** the unit word localizes to "Meile"; the imperial-vs-metric unit CHOICE itself is a separate,
legitimate account setting and not in scope here.
**Screenshot:** `../evidence/diary_de_full.png`.

---

## B19 — [P2] Trends (`/activity-stats`) page mostly unlocalized; inconsistent even within itself
**Type:** Localization · **Layer:** Frontend
**Where:** Trends detail page, reached from Diary → "Trends ansehen".

**Description & proof:** The metric switcher ("Schritte"/"Aktive Minuten") and the app shell (nav, footer)
correctly stay German here, ruling out a session-wide language revert (contrast with B16, where nav/footer
DID regress on Community). Almost everything else on this page's own content is English:
- Range tabs "Week"/"Month"/"Year" — all 3 stay English in every state.
- Chart title "Steps Overview" / "Active Minutes Overview" (per metric) — English.
- "Activity Details" section header, "Today, [date]", value label "Steps Covered" — English.
- Year-view month abbreviations "Jan Feb Mar…Dec" — English — **while** a nearby label "Dieser Monat" (This
  month) on the very same Year view correctly renders German, proving a partial/inconsistent wire-up rather
  than a blanket no-i18n gap.
- Recurs on this page: weekday-axis abbreviations (B7, Week view), "Week 1…Week 5" (B4, Month view), and
  "hrs"/"mins" units (B6, Active Minutes value).

**Expected:** all page-owned strings translate, consistent with the metric switcher and shell that already do.
**Note/Doubt:** root cause not confirmed — most likely explanation is this page's own component(s) shipping
without complete i18n keys (the "Dieser Monat" outlier shows SOME strings here were externalized and others
weren't, i.e. partial coverage, not zero coverage). Unlike B16 (Community), the shell (nav/footer) is
unaffected here, so whatever mechanism drags nav/footer to English on Community does not reproduce on
Trends — these are two distinct bugs with a superficially similar symptom, not the same root cause. Needs
dev confirmation. [FE]
**Screenshot:** `../evidence/trends_de_week_view.png`, `../evidence/trends_de_year_view.png`.

---

# Cross-module consistency analysis (context · word · tone)

**Updated 2026-07-28** — this pass now covers **all 5 Fit modules in German** (Summary, Challenges,
Programs, Community, Diary/Trends; fr/es/pt spot-checked on Summary only). Run per SKILL §11, analysing the
strings already captured during each module's execution — no extra browser driving needed for this section.

### Tone / register consistency
- **German formality is mixed** — see **B12**, confirmed on **3 surfaces**: "Ihr neuestes Abzeichen"
  (Summary, also reused on Community's badge widget), "Ihre umfassenden Wellness-Bedürfnisse" (Programs →
  Offerings), "Ihren Körper" (Programs → bite-size content body) — vs. informal *du* everywhere else,
  including Diary ("Erfasse **deinen** Schlaf"). The bite-size-content instance shows the split reaches
  authored content copy, not just FE chrome.
- Elsewhere the voice is consistently informal/imperative ("Sieh…", "Scanne…", "Tritt… an und verfolge…",
  "Erfasse deinen Schlaf…"), which is fine **once** the stray "Ihr/Ihre/Ihren" instances are fixed.
- Community and Trends can't be scored for register — their own chrome is in English (B16/B19), so there's no
  German prose there to check yet; re-run this check once those bugs are fixed.
- fr/es/pt: no register split observed (those languages don't carry the T–V distinction as visibly here),
  but fr/pt have a **casing** inconsistency (see below / B8).

### Word consistency (same concept → same term?)
- ✅ **Consistent:** "Rang" for *rank* (Wöchentlicher Rang / Gesamtrang), "Fortschritt" for *progress*
  (Wöchentlicher / Meilenstein- / Gesamter Fortschritt), "Community" as a loanword throughout (correctly
  untranslated on every nav tab **and** as Community's own page heading — a deliberate, consistently-applied
  choice, not a defect), "Vantage Fit" (brand) — used the same way in every module.
- ❌ **"challenge" rendered two ways:** nav tab left English "**Challenges**" (B3) while body copy uses German
  "**Herausforderung**" (E-Marathon-/Renn-Herausforderung). Same concept, two words → pick one.
- ❌ **"week" treated two ways:** standalone badge "**Week 1**" left English (B4) while the adjective is
  translated ("**Wöchentlicher** Rang/Fortschritt"). Same root handled inconsistently — and on Trends'
  Month view, the SAME split repeats as "Week 1…Week 5" axis labels.
- ❌ **NEW — "steps"/"active minutes" split by component, not just by string:** Diary and the Trends metric
  switcher both say "Schritte"/"Aktive Minuten" (German), but the Trends chart content immediately below the
  switcher says "Steps Overview"/"Steps Covered"/"Active Minutes Overview" (English) — the *same concept*,
  translated one way in the picker and left English one screen-region away. This is the clearest evidence
  yet that these are wire-up gaps in specific components, not a vocabulary disagreement.
- ⚠️ **fr/pt casing:** "Minutes Actives" vs "Minutes actives" / "Minutos Ativos" vs "Minutos ativos" (B8) —
  same label, two capitalizations across cards.

### Context / coherence (mixed-language within one context)
- ❌ **Mixed language inside one phrase:** "Aktualisiert am **14 Jul 2025**" (Summary, B1); the same pattern
  recurs as "Heute · **28 July 2026**" (Diary) — German word + English-formatted date, every time.
- ❌ **Mixed language inside one card:** Challenge card shows German "Wöchentlicher Rang" beside English
  "Week 1" (B4) → jarring within a single component.
- ⚠️ **"Wellness Score"** English label in an otherwise-German Health card (B9 — judgment: brand or translate?).
- ✅ Brand token "Vantage Fit" correctly kept English inside translated sentences (by design).
- ❌ **NEW — the "reverse" pattern: a lone correctly-German string stranded inside an all-English view.**
  Community's post-feed empty state ("Es gibt keinen Beitrag") is the only German string on an otherwise
  fully-English page (B16); Trends' Year view has "Dieser Monat" as the only German label beside English
  month abbreviations and chart titles (B19). **This is diagnostically useful, not just another instance of
  mixed language:** if the account's language had genuinely reverted to English on these routes, these
  shared/reused strings would be English too, since they pull from the same i18n context as everything else.
  Their surviving in German is the strongest evidence that B16 and B19 are **wire-up/mounting bugs specific
  to those routes**, not a session-wide language revert (B11) recurring — see the updated Note/Doubt on both.

### Consistency verdict
Terminology is largely disciplined where components ARE localized (rank/progress/community/steps-in-the-
switcher all consistent), but the picture across all 5 modules is: **(1)** mixed formal/informal register in
German, now on 3 surfaces including authored content copy **[B12]**; **(2)** the same concept shown in two
languages in one place — "Week 1"/German label, German date-prefix/English date, "Schritte"/"Steps Overview"
**[B1/B4]**; **(3)** tab-vs-body word split for "challenge" **[B3]**; and **(4)**, newly surfaced by running
this check module-by-module, **two entire routes (Community, Trends) where the module's own chrome never
localizes at all**, distinguishable from a language revert precisely because a handful of shared strings on
each route correctly stay German **[B16, B19]**. fr/pt add a minor casing inconsistency **[B8]**. Recommend:
a single glossary + register decision applied product-wide, PLUS a targeted audit of Community's and Trends'
component trees for missing/broken i18n wiring — these two are now the highest-value fix targets, well above
polish-level issues like B8/B9.
