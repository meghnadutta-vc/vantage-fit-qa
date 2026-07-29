---
name: localization-testing
description: >
  Localization (i18n) QA for the EMPLOYEE-FACING Vantage Fit web app
  (app.vantagecircle.co.in/ng/fit/*, the heart icon), driven via Playwright MCP. Use when testing
  translations / multi-language UI on the employee Fit web or app: the 5-module map with current
  coverage, the profile-based language switch that forces a logout/re-login, the runtime
  language-desync trap that invalidates naive results, per-screen + cross-module consistency
  checklists, frontend-vs-backend classification without a fetchable dictionary, and the visual
  re-review step that text extraction cannot replace. Produces QA docs under Localization/web/ with
  sequential bug IDs B1…B28. For the ADMIN DASHBOARD (dashboard-v2.vantagecircle.co.in/fit/*) use
  `dashboard-localization-testing` instead. This skill FINDS bugs; it does not file them — reporting and
  ticket creation are out of scope here, see `localization-bug-reporting`.
---

# Localization Testing — Employee Vantage Fit **Web / App**

You are a Senior QA Engineer running localization validation on the **employee-facing** Vantage Fit web
app, driven with **Playwright MCP**. All artifacts live under `Localization/web/`.

## Which surface am I testing? (read this first — two engagements exist)

| | **This skill** | The `dashboard-localization-testing` skill |
|---|---|---|
| Surface | **Employee** Fit web / app (heart icon) | **Admin** dashboard (HR admin) |
| URL | `app.vantagecircle.co.in/ng/fit/*` | `dashboard-v2.vantagecircle.co.in/fit/*` |
| Docs | `Localization/web/` | `Localization/dashboard/` |
| Bug IDs | **`B1`…`B28`** (sequential) | `OV#1`, `CC#2`, `RPT#4`… (module-prefixed) |
| Modules | **5** (see §2) | 19 |
| Language switch | **Profile setting → forces logout/re-login** | Sidebar `<select>`, no re-login |
| Dictionary | **Not fetchable** — the JSON path returns the SPA shell (B10) | Fetchable, 991 keys × 18 |
| Backend defects | **5 found** (B14, B23, B24, B26, B27) | 0 (backend out of scope there) |

**If the URL contains `dashboard-v2`, you are on the wrong skill.** Stop and switch.

**Reporting:** this skill covers *finding* bugs. To turn findings into a categorised report or Jira
tickets, use **`localization-bug-reporting`**.

---

## 0. Golden rules (non-negotiable)

1. **Never assume expected behaviour.** Derive it from the rendered English baseline, API responses, or
   product requirements. If none is available → mark **"Needs Verification"** or **"Needs Product
   Confirmation"**, never guess.
2. **Module-level quality does NOT transfer between languages.** The single most important lesson from this
   engagement — see §5. Every **(module × language)** pair needs independent verification.
3. **Do a visual re-review of every screenshot.** Text extraction does **not** surface overlap, broken
   images, or clipping. Two bugs (B22, B23) sat unnoticed in already-captured evidence until a dedicated
   visual pass. Make it a standing step, not a fallback.
4. **Verify bug IDs before citing them.** "Challenges tab untranslated" was cited as B5 across 6 files for
   days; its real ID is **B3** (B5 is Highlights social strings — unrelated). Check the consolidated log.
5. **Never print, echo, or persist credentials** anywhere — chat, logs, files, screenshots.

---

## 1. Access & language switching (the expensive part — plan around it)

**Login:** go to `https://api.vantagecircle.co.in/` → click **Login** (native email/password form; *not*
"Login via OTP", *not* Microsoft) → credentials from `qa-credentials.local.txt` → lands in the perks app
`app.vantagecircle.co.in/ng/home` → click the **heart icon** (Vantage Fit) → `/ng/fit/summary`.

Direct-navigating to the app root forces **Microsoft Azure AD SSO**, where the password fails —
**that is a dead end, avoid it.** If credentials are rejected or the file is missing/empty, **STOP and
ask** — never guess accounts.

**Tenant:** UAT test account. Apply blast-radius control on anything that writes or is outward-facing.

### ⚠️ Changing language costs a full re-login

```
Profile avatar → View Profile → My Info → Edit Profile → Language → Save
  → confirmation alert  (this is where B2 lives — capture its text)
  → FORCED LOGOUT
  → re-login natively via api.vantagecircle.co.in
  → navigate back to /ng/fit/…
```

There is **no in-page language switcher.** Consequences for how you plan a run:

- **Batch by language, not by module.** One switch, then sweep all 5 modules before switching again.
  Switching per module multiplies re-logins for nothing.
- **Capture the language-change alert every time** — it is a test surface in its own right (B2: the
  translated strings print a literal `{language}` token in de/fr/es/pt; only English interpolates).
- **Expect natural session expiry mid-run.** When it happens, check what language comes back *before* doing
  anything else — that is bug **B11** (preference not persisted; reverts to English after re-login).
- **Many switches in one day appear to accumulate staleness.** The French pass opened *already* in the B25
  English-fallback state on its very first fresh load. Don't read that as "French is worse" — note the
  session history instead.

---

## 2. The 5 modules — routes and coverage

| # | Module | Route | Coverage | Known bugs |
|---|---|---|---|---|
| 1 | **Summary** | `/ng/fit/summary` | de, fr, es, pt + en baseline | B1–B10, B12 |
| 2 | **Challenges** | `/ng/fit/challenges/(challengesOutlet:listing)?tab=ongoing` | all 4 langs | B3, B4, B21, B27 |
| 3 | **Programs** | `/ng/fit/programs` (Library + Offerings sub-tabs, content detail) | all 4 langs | B11, B13, B14, B15, B23, B24 |
| 4 | **Community** | Social + Events sub-tabs | all 4 langs | **B16** (own chrome 0% localized) |
| 5 | **Diary / Trends** | Diary `/ng/fit/summary/diary` (via Summary's Snapshot card) · Trends `/ng/fit/activity-stats` (via Diary's Snapshot card) | all 4 langs | B17, B18, B19, B20, B22, B28 |

**Languages:** de, fr, es, pt tested + English baseline. The app requests
`/ng/assets/i18n/fit/{en,fr,es,pt,pt-BR,pt-PT,de}.json` — **7 locales wired.** The profile offers more;
**12 further profile languages are untested, including Arabic (RTL) — the highest-risk untested language.**

**Servers:** India only. US / Europe / E2E untested.

**Bug totals as of 2026-07-28:** 28 bugs — **P2: 15 · P3: 11 · P4: 2** · **FE: 22 · BE: 5 · FE/BE TBD: 1.**
Both the French and Portuguese passes added **zero new bug IDs** — every finding confirmed an existing bug
recurring. That is a valuable result, not a wasted pass: it is what makes the shared-cause case strong
(B16, B22, B23, B27 confirmed in **all 4** languages; B12 in 3 of 4).

---

## 3. Per-module 4-phase workflow

1. **Discover & design.** Map every screen, sub-tab, card, dialog, empty/loading/error state and the APIs
   involved. Write cases to `test-cases/<module>.md` (columns per `CLAUDE.md`).
2. **Execute.** Run per language on **fresh loads**. Fill Actual / Status / Notes. Unverifiable →
   "Needs Verification" / "Needs Product Confirmation".
3. **Bugs.** Log to `bugs/<module>.md` and the consolidated `bugs/bug-log.md`, grouped P1/P2/P3, each
   classified FE / BE / FE-BE TBD (§4). **IDs are sequential `B<n>` across the whole engagement** — never
   restart per module, and check the consolidated log for the next free number.
4. **Report.** Update `Execution_Status.md` + `Coverage_Matrix.md`, name the next module, **STOP for
   confirmation.**

---

## 4. Frontend vs Backend classification — no fetchable dictionary here

**`/ng/assets/i18n/fit/<lang>.json` returns the SPA HTML shell, not JSON** (bug **B10**, an infra issue) —
yet translations still render. So you **cannot** flatten a dictionary and look strings up by value on this
surface. Classify by other means:

- **Compare against the English baseline.** Capture English first for every module. A string identical in
  English and the target language *and* that is UI chrome → **FE gap**. Identical *and* it is a challenge
  name, post title, username or library content title → **authored/BE data, expected, not a bug.**
- **Check the API response body.** If the string arrives as data (`label`, `title`, an answer option) it is
  backend. This is how **B26** was confirmed — the adherence answer "Yes" comes from the `configuration`
  API and should be "Sí".
- **Look for the same concept rendering differently in two places.** If one surface shows the translation
  and another shows English, the translation **exists** → **FE wire-up gap**, not a missing translation.
  B3's proof: German body text says "Herausforderung" while the nav tab says "Challenges".

**Backend defects DO exist on this surface.** Confirmed: **B14** (empty category grid — locale-handling gap
on a paginated endpoint, `GET /content/category/20` empty while `POST /content/byCategoryName` has content),
**B23** (malformed CDN image URLs), **B24** (intermittent 502 on `/marketplace/categories`), **B26**
(untranslated API answer option), **B27** (garbled water-task sentence).

**Always expected to stay as authored — not bugs:** brand tokens ("Vantage Fit"; "Wellness Score" pending
B9's product call), challenge names, community post titles, usernames, and library content titles.

---

## 5. The traps that invalidate results — read before every run

### B25 — runtime language desync (the big one)

The effective/runtime language **observably desyncs from `<html lang>` and the saved profile preference
mid-session, with no re-login and no language change.** Reproduced on 4 consecutive fresh loads and
confirmed on Summary, Programs **and** Challenges — not only the modules that first suggested it.

**It corrupts backend content queries too**, not just chrome: Programs' Library served the full
English-baseline content set instead of the Spanish-scoped set seen earlier the same day.

**Consequences:**
- Any ✅ is **point-in-time only**. Re-check a sample late in a long session.
- It likely explains B14 / B16 / B19 / B20 as symptoms of one mechanism rather than four per-module gaps —
  Community looks like the deterministic case, the others intermittent.
- If a whole module suddenly reads English, **suspect B25 before logging a new bug.**

### The "reverse signal" — route bug vs session-wide revert

A **lone correctly-translated string stranded inside an otherwise all-English view** (Community's empty
state, Trends' "Dieser Monat") proves the *session* language is fine and the **route's own chrome** is
unwired. Use it to separate a real per-route wire-up bug (B16, B19) from a session-wide revert. Confirm the
other way too: load a known-good module in the same session.

### Module quality does not transfer between languages

**B14 is German-only** (Spanish is fine). **B20 is Spanish-only** — and Diary, the *best*-localized screen
in German, is **~90% English in Spanish including the nav bar**. A German-only pass therefore systematically
misreports this surface. **Verify each (module × language) pair independently.**

### Content-name false positives

An English-month regex will match **authored data** — "Announcement 17 Sep" is a challenge name, not a UI
date. **Exclude known content titles** (diff against the English-baseline content list) before flagging any
date or mixed-language finding.

---

## 6. Per-screen checklist

Full version: `Localization/web/Localization_Checklist.md`. Apply to **every screen, in every language**;
mark ✅ / ❌ (→ bug id) / ⚠️ judgment / N/A.

**Strings:** nav & tabs (incl. sub-tabs Library/Offerings) · section & card headings (Snapshot, Trends,
Vitals, Health, Highlights, Featured Content, Health bites) · metric and field labels (Steps, Active
Minutes, Avg Sleep, Weekly Rank/Progress, Wellness Score, Hemoglobin) · buttons/CTAs · subtitles & helper
text · placeholders & tooltips · **empty / loading / error states** · toasts and validation messages ·
**dialogs & alerts, including placeholder interpolation** (`{language}` → B2) · footer.

**Locale formatting:** dates (format **and** translated month/weekday names) · relative time
("2 days ago") · **units** (mins, sec, hrs, /day, g/dL, mile, fl oz) · numbers, percentages, currency ·
**chart axis labels** (weekday initials "S M T W T F" → B7).

**Layout & a11y:** `<html lang>` matches the selection · truncation/overlap (**de and fr run ~20% longer** —
sweep chips, badges and fixed-width pills; B22 is a fixed-width selection pill overflowing) · aria-labels on
icon buttons · focus order.

**Unit toggles need every value AND every label checked.** B28: switching Log Water to fl oz converts the
value and the slider but leaves "1 glass = 250 ml" metric. Partial conversion is a data-integrity class of
bug, not cosmetic.

---

## 7. Cross-module consistency pass (once per language, after string capture)

Run on the **captured dumps** — no extra browser driving needed. Method validated 2026-07-28: run against
three already-logged modules it re-derived B1, B3, B4, B6, B9 and the new B12 with no misses.

**A. Tone / register.** One politeness register product-wide. **Vantage Fit's default voice is informal
`du` / `tu` / `tú` / `você`.** Grep for formal (`Ihr/Ihre/Ihnen/Sie`, `vous/Votre/vos`, `usted/Su/sus`) and
informal (`du/dein/dich/dir`) markers — **both present = defect.** B12 is exactly this, and it recurs on the
**identical 3 structural positions** in de, es and fr ("Ihr/Votre/Su neuestes Abzeichen", the needs
sentence, the imperatives) — which suggests a **shared source string** rather than three independent
translation slips. Portuguese was checked and doesn't clearly apply (no competing informal form found) —
record that as a documented linguistic reason, **not** a pass. Also check button voice is consistently
imperative or infinitive.

**B. Word / terminology (glossary).** Same concept → same term across modules: rank, progress, challenge,
week, streak, points, badge, activity, community. Flag:
- one concept in **two languages** (nav EN "Challenges" vs body DE "Herausforderung" → **B3**)
- **two different words in the same language** (nav "Retos" vs body "Desafío" → **B21** — a glossary gap,
  mechanically different from B3's missing translation)
- the same root handled differently ("Week 1" vs "Wöchentlich…" → **B4**)
- a **component-level** split ("Schritte"/"Aktive Minuten" in the switcher vs "Steps Overview"/"Active
  Minutes Overview" in the content)
- **casing** splits ("Minutes Actives" vs "Minutes actives" → **B8**)
- loanwords (Community, Wellness Score) used consistently, or flagged as brand/judgment

**C. Context / coherence.** No mixed language **within one phrase** ("Aktualisiert am 14 Jul 2025" → B1) or
**within one card** (German labels beside English "Week 1"). Placeholders resolve in context. Units and
dates read naturally in the translated sentence (**B27**: "fl oz vasos", "1 días" pluralization).

**Careful with regex on non-Latin or accented text:** JavaScript `\b` is ASCII-only and misfires. Use
Unicode-aware boundaries: `new RegExp('(^|[\\s\\p{P}\\p{S}])'+w+'($|[\\s\\p{P}\\p{S}])','iu')`.

**Report as** a "Cross-module consistency analysis" section in `bugs/bug-log.md` with three buckets
(Tone/register · Word/terminology · Context/coherence), each ✅/❌/⚠️, cross-referencing bug IDs. New
standalone defects get a new `B<n>`; consistency *views* of existing bugs just reference them. Recommend
**one glossary + register decision applied product-wide** as the fix.

---

## 8. Playwright notes for this app

- **Take a screenshot at every distinct state** into `evidence/`. Naming in use: `<module>_<lang>.png`,
  `<module>_<lang>_<state>.png`, and per-bug crops `bug_B<n>_<what>_<lang>.png`. Reference the filename in
  the test case and in the bug.
- **Capture the English baseline first** for every module — it is what makes FE/BE classification and
  translation-length analysis possible at all on this surface.
- **Toasts are transient** → install a MutationObserver *before* the action and wait ~2s before reading.
  Reading immediately yields a false "no toast". If observer timing is uncertain, record the result as
  **inconclusive** rather than asserting a defect (this happened with the Log Water success toast).
- **Native browser alerts** (the language-change confirmation) cannot be screenshotted — capture the text
  verbatim as proof and say so in the bug (that is how B2 is evidenced).
- `browser_evaluate` returning a whole element's `textContent` can blow the token limit — filter to
  `children.length === 0` leaf nodes and cap string length.
- Refs go stale after navigation/re-render — re-snapshot. Snapshots sometimes return empty mid-load → wait
  ~2s and re-snapshot.
- Some screens are reachable only by **card navigation, not by URL**: Diary via Summary's Snapshot card,
  Trends via Diary's Snapshot card.

---

## 9. Deliverables (append, never overwrite prior runs)

```
Localization/web/
  bugs/bug-log.md            # consolidated: FE section + BE section, B<n> IDs, detail, consistency analysis
  bugs/<module>.md           # per-module bugs
  test-cases/<module>.md     # per-module cases
  evidence/<module>_<lang>.png · bug_B<n>_<what>_<lang>.png
  Execution_Status.md        # per-module phase table + dated run history + bug counts
  Coverage_Matrix.md         # module × dimension
  Localization_Checklist.md  # the full per-screen + consistency checklist
  <Language>_Pass_Conclusion.md   # one per language pass
```

**End every run with:** modules/screens covered (done / partial / blocked), bug counts by severity, and an
explicit list of **what was NOT done and why** — blocked flows, Needs-Verification items, languages and
servers not covered.

---

## 10. Open frontier (biggest first)

1. **The language axis.** Only 4 of the profile's languages are tested. **Arabic (RTL) is the highest-risk
   untested language** — nothing on this surface has ever been checked for RTL behaviour.
2. **The server axis.** India only; US / Europe / E2E untested.
3. **B25 root cause.** The desync is observed and reproduced but not explained. Until it is, every
   per-module result carries an asterisk.
4. **Systematic re-verification.** 28 bugs logged, no regression pass has been run.
