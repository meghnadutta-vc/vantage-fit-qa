# Vantage Fit Web — German Localization Pass — Conclusion (2026-07-28)

**Scope of this document:** wraps up the German (de) pass across **all 5 Fit modules** — the priority set
for today. Languages beyond German (fr/es/pt/others) and servers beyond India remain open; see "What's next".

## Coverage — all 5 modules now have a German pass

| Module | German coverage | Status |
|---|---|---|
| Summary | Full screen (2026-07-24) | ✅ Strong — 10 bugs, mostly polish-level |
| Challenges | Main listing, ongoing tab (2026-07-24) | ✅ Strong — 0 new bugs, recurrences only |
| Programs | Library + Offerings + content detail (2026-07-24, 2026-07-28) | ✅ Strong — recurrences + 3 new bugs, one B12 register bug reaches content copy |
| Community | Social + Events sub-tabs (2026-07-28, new) | ❌ **Weak — 0% of its own chrome localizes (B16)** |
| Diary / Trends | Diary + activity-stats detail (2026-07-28, new) | Diary ✅ **best-localized screen found**; Trends ❌ **mostly unlocalized (B19)** |

**Headline result:** German localization quality is **bimodal**, not uniformly good or bad. Screens built
earlier/matured longer (Summary, Challenges, Programs, Diary) localize well, with only scattered
string-level gaps. Two specific routes — **Community** and the **Trends detail page** — have their own
chrome almost entirely unlocalized, while still correctly inheriting German for shared/reused components.
That inheritance is the tell: it rules out a session-wide problem and points to route-specific wiring gaps.

## Bug tally (all modules, German pass complete)

- **P2 (11):** B1 (dates), B2 (`{language}` placeholder), B3 (Challenges tab), B4 ("Week 1"), B5 (Highlights
  social strings), B11 (language not persisted), B12 (register mixing, 3 surfaces), B14 (empty content grid),
  B16 (Community chrome unlocalized), B17 (caloric-deficit sentence), B19 (Trends chrome unlocalized).
- **P3 (6):** B6 (units), B7 (weekday axis), B8 (fr/pt casing), B13 ("Written By"), B15 (CTA overlap),
  B18 ("mile" unit word).
- **P4 (2):** B9 ("Wellness Score", judgment), B10 (i18n JSON infra issue).
- **By layer:** 16 Frontend · 1 Backend (B14, TBD) · 2 FE/BE-TBD (B11, B15).

## Fixed today, not a new bug: bug-ID mislabeling
"Challenges tab untranslated" had been cited as **B5** across 6 files since 2026-07-24 (test-cases and
bug-logs for Challenges/Programs, the checklist, the skill-test-run doc, and the consolidated log's own
consistency section). Its real ID is **B3** — B5 is the unrelated "Highlights social strings" bug. Corrected
everywhere; verified no remaining mislabels (`grep -rn "B5"` now only matches genuine B5/Highlights lines).

## What the §11 consistency skill added this pass
Running the skill's tone/word/context method **across all 5 modules together** (not just re-reading
per-module notes) surfaced two things no single-module pass would have:
1. **B12 (register mixing) reaches authored content copy**, not just UI chrome — found in the Programs
   bite-size article body ("Ihren Körper…"), on top of the two chrome-level instances already known.
2. **The "reverse" diagnostic signal** — a shared/reused string staying correctly German inside an
   otherwise all-English view (Community's empty-state text; Trends' "Dieser Monat") — which is what let us
   rule out a session-wide language revert for B16/B19 and narrow both to route-specific wiring bugs. This
   technique is now written into `SKILL.md` §11 for reuse on future modules.

## Recommended priority for developers
1. **B16 (Community) and B19 (Trends)** — highest value: two entire routes with no localization, versus
   scattered string gaps elsewhere. Fixing these closes the single biggest coverage hole in German.
2. **B12 (register)** — a 3-line copy fix (Ihr→Dein, Ihre→Deine, Ihren→Deinen) resolves the most visible
   tone inconsistency across the whole product.
3. **B11 (persistence)** and **B14 (empty content grid)** — functional/data bugs, not pure translation gaps,
   but both P2 and both discovered during this localization work.
4. Everything else (B1–B9, B13, B15, B17, B18) — scattered string/format-level gaps, lower individual impact.

## What's next (not done, explicitly)
- **French / Spanish / Portuguese** passes for Programs (Offerings/detail), Community, and Diary/Trends —
  not started; only Summary has full fr/es/pt coverage, Challenges has pt only.
- **Other profile languages** (16 offered, incl. Arabic = RTL, the highest-risk untested language) — untested.
- **US / Europe / E2E servers** — not started; all work so far is India (`app.vantagecircle.co.in`) only.
- **Dynamic-flow/functional localization** beyond what was click-tested today (toasts, validation messages,
  Vitals edit flows, Log Water flow, Events "create event" if it exists, Challenges create-flow).
- **Root-cause confirmation** for B14, B15, B16, B19 — all flagged FE/BE-TBD or "needs dev confirmation";
  an English-baseline comparison of the same routes would help distinguish "always broken" vs "German-only".

## Addendum (added after the Spanish pass, same day)
Two further bugs were found via a follow-up visual re-review of screenshots already captured during THIS
German pass — a reminder that text-content extraction alone misses purely visual defects:
- **B23 (P2):** ~28 Programs content-image URLs 404 (malformed CDN paths), rendering nearly every Library/
  Offerings thumbnail as a solid black box. Visible in `programs_de_offerings_tab.png` above but not caught
  until prompted by the user during the Spanish pass.
- **B22 (P3, user-found):** the Trends Steps/Active-Minutes toggle's selection pill overlaps the neighboring
  tab's text — visible in `trends_de_week_view.png` above, also missed until flagged by the user.

Both are detailed in the consolidated `bugs/bug-log.md` and in `Spanish_Pass_Conclusion.md`, which also
covers B24 (an intermittent Offerings-tab 502). See the skill's §8 update for the process fix (screenshots
now get a deliberate visual pass, not just DOM-text extraction).

## Deliverables touched today
`test-cases/{programs,community,diary-trends}.md` (new/extended) · `bugs/{programs,community,
diary-trends,bug-log}.md` (new/extended, B13–B19 added) · `Execution_Status.md` · `Coverage_Matrix.md` ·
`Localization_Checklist.md` · `.claude/skills/localization-testing/SKILL.md` (§11 refined) · 10 new
evidence screenshots · this document.
