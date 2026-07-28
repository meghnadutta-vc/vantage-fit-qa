# Vantage Fit Web — Spanish Localization Pass — Conclusion (2026-07-28)

**Scope of this document:** wraps up the Spanish (es) pass across **all 5 Fit modules**, run the same day as
the German pass to deliberately cross-check which German findings are systemic vs. language-specific.
French and Portuguese remain partially covered (Summary/Challenges only); servers beyond India remain open.

## Coverage — all 5 modules now have both a German AND a Spanish pass

| Module | Spanish coverage | Status |
|---|---|---|
| Summary | Full screen (2026-07-24 baseline, re-verified fresh 2026-07-28) | ✅ Strong |
| Challenges | Main listing, ongoing tab | ✅ Strong — 0 functional bugs, 1 new terminology bug (B21) |
| Programs | Library + Offerings + content detail + "View all" | ✅ Strong — confirms 3 German bugs recur, 1 does not |
| Community | Social + Events sub-tabs | ❌ **Weak — identical failure to German (B16), 0% of own chrome localizes** |
| Diary / Trends | Diary + activity-stats detail | Diary ❌ **~90% English (B20, new) — opposite of German** ; Trends ❌ mostly unlocalized, worse than German (nav also breaks) |

## Headline result: this pass answers "is it German-specific or systemic?" for 5 open bugs

Running the same test in a second language was the point of today's work, and it resolved every open
Note/Doubt from the German pass:

| Bug | German-only test said | Spanish result | Conclusion |
|---|---|---|---|
| **B3** (Challenges tab untranslated) | Only tested in German | Spanish correctly shows "**Retos**" | **German-specific** — a missing/mis-wired key for that one locale |
| **B12** (register mixing) | 3 surfaces in German | Same 2 structural surfaces recur in Spanish ("Su"/"sus") | **Systemic** — likely a shared source string, not a per-language slip |
| **B13** ("Written By") | Assumed hardcoded, unconfirmed | Recurs identically in Spanish | **Confirmed hardcoded FE string** |
| **B14** (Programs "View all" empty grid) | Root cause unknown | Spanish returns a populated grid | **German-specific** — a locale-handling bug on one backend endpoint |
| **B15** (CTA overlap in bite-size content) | Could be a translation-length overflow | Reproduces pixel-identically in Spanish with different text length | **Language-agnostic template bug**, not a translation-length issue |
| **B16** (Community chrome unlocalized) | Root cause unclear | Identical failure in Spanish, including the nav/footer regression | **Systemic module bug**, not a per-locale gap |
| **B19** (Trends chrome unlocalized) | Nav/footer stayed correct in German | Nav/footer **also break** in Spanish on the same page | **Behavior itself is language-dependent** — a new, sharper finding |

## The single most important new finding: B20 (Diary breaks in Spanish, not German)

Diary was called out in the German conclusion as "the best-localized screen in the whole engagement." In
Spanish, the same route is **~90% English**, including the app-shell nav bar — only the reused Snapshot
widget ("Pasos"/"Minutos Activos") survives correctly. This is the clearest possible demonstration that
**a module passing in one language is no signal about whether it passes in any other** — every
(module × language) pair needs its own verification pass. Paired with B14's opposite asymmetry (broken in
German, fine in Spanish), the data now points to a specific technical hypothesis: individual
routes/components are missing complete translation resources for **specific languages**, and when a
required resource fails to load, the failure appears to cascade into resetting a shared locale signal that
the nav bar also reads (explaining why nav breaks alongside content on Community, Diary-in-Spanish, and
Trends-in-Spanish, but not on the same routes in whichever language IS fully translated).

## New bugs found via the Spanish pass
- **B20** (P2) — Diary chrome + nav regress to English in Spanish.
- **B21** (P3) — Spanish "challenge" rendered two ways: nav "Retos" vs body "Desafío" (a glossary
  inconsistency, distinct from B3's missing-translation mechanism).
- **B22** (P3, user-found) — the Trends Steps/Active-Minutes toggle's selection pill is wider than its own
  segment (measured live: 144px pill vs 103.75px segment in Spanish), overflowing ~40px into the
  neighboring tab and covering the start of its text ("Minutos Activos" → "inutos Activos"). Reproduces in
  German too (less visibly, since "Schritte" is a longer label than "Pasos") — a language-agnostic layout
  bug that shorter translations expose more.
- **Content-quality observation (not a bug):** Programs' Spanish library carousel surfaces placeholder-
  looking titles ("Spanish Content", "New SPANISH Updated English Content") — flagged for the content owner.

## Bug tally (all modules, both languages, running total)
- **P2 (13):** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19,B20,B23.
- **P3 (9):** B6,B7,B8,B13,B15,B18,B21,B22,B24.
- **P4 (2):** B9,B10.
- **By layer:** 20 Frontend · 3 Backend (B14 German-specific, B23 broken content images, B24 intermittent
  502) · 1 FE/BE-TBD (B11).

## Addendum: a user-prompted visual re-review found 2 more bugs (B22, B23) plus B24
After this document was first drafted, the user asked "did I miss any such UI bugs elsewhere?" — prompting
a re-inspection of every screenshot captured today with the image tool, rather than relying on the DOM-text
extraction used for the localization checks themselves. That surfaced:
- **B22** (P3) — the Trends toggle-pill overlap (the user found this one directly; see main text above).
- **B23** (P2) — ~28 broken content-image URLs on Programs, rendering nearly every Library/Offerings
  thumbnail as a solid black box. This was sitting in a screenshot from the ORIGINAL German pass
  (`programs_de_offerings_tab.png`) and had been noticed in passing during testing but never logged.
- **B24** (P3) — an intermittent 502 on `/marketplace/categories`, found while re-verifying B23 live.
This is now written into the localization-testing skill (§8): screenshots get a deliberate visual pass
going forward, not just DOM-text extraction, since overlap and broken-image defects don't show up in text
dumps at all.

## Recommended priority for developers (updated)
1. **B16 (Community) and B19/B20 (Trends/Diary in Spanish)** — still the highest-value fix target: three
   routes now confirmed to lose most or all of their own localization in at least one language, with the
   shared-widget "reverse signal" evidence pointing at a locale-resource-loading bug, not scattered typos.
2. **B12 (register)** — now confirmed a 2-string fix (not per-language) that resolves the tone inconsistency
   across German AND Spanish at once if fixed at the source-string level.
3. **B14** — now scoped precisely to German's locale handling on one endpoint; a narrow backend fix.
4. **B21** — a copy/glossary decision (Reto vs Desafío), not an engineering fix.
5. Everything else (B1, B4, B6–B9, B13, B15, B17, B18) — scattered string/format-level gaps, confirmed to
   recur across languages, lower individual impact than the above.

## What's next (explicitly deferred)
- **French** — only Summary has a fr pass; Challenges/Programs/Community/Diary-Trends untested in fr.
- **Portuguese** — Summary and Challenges have pt; Programs/Community/Diary-Trends untested in pt.
- **Other profile languages** (12 more offered, incl. Arabic = RTL, the highest-risk untested language).
- **US / Europe / E2E servers** — still India-only for every module and language tested so far.
- **Root-cause confirmation** for B16/B19/B20 — the shared-locale-cascade hypothesis is well-evidenced but
  not verified against the actual codebase; a dev should check whether Community/Diary/Trends' i18n
  namespaces have complete resource files per language, and whether a failed load resets a shared signal.
- Dynamic-flow/functional testing beyond what was click-tested (Vitals edit, Log Water, create-flows) —
  same gaps as the German pass, not yet covered in either language.

## Deep-dive re-pass (2026-07-28, "do not miss anything") — a second addendum

The user asked for a more thorough Spanish pass, explicitly not to miss anything. This covered every
sub-tab, functional flow, and dynamic state not reached by the first pass: Challenges' Upcoming/Past tabs
and a challenge detail page, Programs' category filters and content-refresh behavior, Community's Events
tab (explicit re-check), and Diary's Vitals-edit/Log-Water/date-stepper flows.

### The headline finding: B25 — effective language desyncs from `<html lang>` mid-session, no re-login
While re-testing Summary — already confirmed fully Spanish earlier the same day — it came back **partially
English** (nav + section headings) on 4 consecutive fresh loads, with **no re-login and no language change**
in between. `<html lang>` still read "es" and My Info still showed Spanish saved. The same session then
showed **Programs' Library switch from serving Spanish-scoped content to the full English-baseline content
set** — proving the desync reaches backend content queries, not just FE chrome strings. This reframes the
whole engagement's understanding of B14/B16/B19/B20: instead of four separate per-module translation gaps,
the evidence now points to **one shared mechanism** — an effective/runtime "current language" value that can
silently diverge from the saved preference — with Community as a apparently permanent/deterministic case of
it, and Trends/Diary/Summary/Programs as an intermittent one. **Practical implication: a module passing a
language check once is not a guarantee it still passes later in the same session.**

### Three more bugs found in the same pass
- **B26** — the backend `configuration` API's adherence-activity answer options are `["No","Yes"]`; "No"
  is coincidentally correct in Spanish, but "Yes" should be "Sí" and isn't.
- **B27** — a challenge's water-intake weekly task reads "Beba al menos 67.6 fl oz vasos de agua 1 días esta
  semana": an untranslated imperial unit, a nonsensical unit+count combination ("fl oz vasos"), and a
  pluralization error ("1 días" should be "1 día") — while the sibling steps/strength tasks on the same
  challenge are correctly formed. Also newly found on this same screen: task instructions use **formal
  "usted" imperatives** (Camine/Beba/Registre), a 3rd Spanish surface for the register-mixing bug **B12**.
- **B28** — Log Water's "1 glass = 250 ml" helper label doesn't convert when the modal's unit toggle is
  switched to fl oz, even though the main value and slider do.

### Functional checks — all clean
Category-filter selection, mood-edit modal, water-logging submission (verified via before/after data read),
date-stepper navigation, and challenge-detail navigation all worked correctly with no functional breakage or
layout overlap. Two items were inconclusive rather than confirmed-clean: the Challenges "+Add" entry point
didn't visibly open a menu on the one attempt (not forced further, per blast-radius guidance), and a
Log-Water success-toast capture attempt didn't wait long enough to be conclusive.

### Updated bug tally (all modules, both languages, post deep-dive)
- **P2 (15):** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19,B20,B23,B25,B27.
- **P3 (11):** B6,B7,B8,B13,B15,B18,B21,B22,B24,B26,B28.
- **P4 (2):** B9,B10.
- **28 bugs total.** By layer: 22 Frontend · 5 Backend (B14,B23,B24,B26,B27) · 1 FE/BE-TBD (B11).

## Deliverables touched today (Spanish pass, both rounds)
`test-cases/{challenges,programs,community,diary-trends}.md` (extended) · `bug-logs/{challenges,programs,
community,diary-trends,bug-log}.md` (extended — B12/B13/B14/B15/B16/B19 updated, B20-B28 added) ·
`Execution_Status.md` · `Coverage_Matrix.md` · 16 new evidence screenshots · this document.
