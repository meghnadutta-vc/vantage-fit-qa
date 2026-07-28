# Remaining Work — Vantage Fit Dashboard Localization

**Purpose:** live per-language completion tracker. Update as items close. Companion to
`GAP_REGISTER.md` (which explains *why* each gap matters); this file answers *"what is left, per language."*

Last updated **2026-07-28**.

---

## GERMAN (de) — status as of 2026-07-28

German is the deepest-tested language, but "done" applies to **2 of ~22 dimensions**, not the whole matrix.

### ✅ Done
| Dimension | Coverage |
|---|---|
| Translation / string rendering | All 19 modules, deep pass (2026-07-21/22) |
| Dynamic flows (validation, toasts, live submits) | 3 runs — **German is the only language with this** |
| Functional navigation | 24/24 sidebar routes verified |
| **UI breaks / truncation / overlap** | All 19 modules @1024, + 1440/1366 spot-checks, **with English controls** (Run 5–6) |
| Email Designer access | Route resolved (modal, not a route); ED#1 confirmed |
| FE vs BE classification | Applied throughout via the 991-key dictionary |

### ❌ NOT done for German — ordered by value

**Tier 1 — do these first:**
- [x] ~~**Cold-load re-verification.**~~ **DONE + DOWNGRADED 2026-07-28.** I initially flagged this as the
      highest-value German task, on the theory that ES#1 (English on cold load, Spanish after in-app nav)
      meant modules clicked-through may have been verified in their good state only. **Measured: 10 of 11
      modules render identically cold and warm.** ES#1 is component-specific, not systemic, so the German
      sign-offs are **not** broadly invalidated and a German cold-load sweep is **low** value. Optional
      residual: cold-load the 3 known-affected components in German (~10 min). Detail + the RPT#1-vs-ES#1
      distinction: `bug-logs/spanish-full-sweep.md`.

**The two credible P1 leads (nothing else in this engagement can produce a P1):**
- [ ] **G5 — comma-decimal *input*.** Display formatting is covered; input is not. German users type `2,5`.
      Test every numeric input (Settings team-size min/max, Upload Points values); submit; verify the stored
      value. Silent truncation/misparse = data integrity. **~45 min.**
- [ ] **G6 — CSV upload with non-ASCII / semicolon delimiters.** Only ASCII was ever uploaded. Test umlaut
      names (Müller, Schröder), localized headers, and the semicolon delimiter German Excel emits by
      default. **~45 min.**

**Tier 2 — German-specific, high yield, cheap:**
- [ ] **G13 — glossary / register / tone pass (§11).** *Never run on the dashboard.* Found a P2 register bug
      plus two terminology splits on the employee web. The dashboard is the deeper German surface with a
      full 991-key dictionary, so expect findings: *Herausforderung* vs *Challenge*, *Sie* vs *du* across
      19 modules, *Mitarbeiter* vs *Angestellte*, *Abzeichen* vs *Badge*. **No browser needed** — analyse
      already-captured strings. **~1 hr.**
- [ ] **G12 — pseudo-localization.** Never used. Hardcoded English is the dominant defect class here
      (13+ bugs), so the not-externalised inventory is currently *"what was noticed"*, not *"what exists"*.
      **~1 hr.**
- [ ] **G1 — runtime language desync re-check.** Re-load 3–4 already-passing modules late in a long session
      and diff. May qualify every existing ✅. **~30 min.**
- [ ] **G3 — language persistence across logout/login.** Set de → logout → login → check. This is a **P2 on
      the employee web (B11)** and was never tested here. **~10 min.**

**Tier 3 — whole dimensions still at zero for German:**
- [ ] **G4** exported file contents (translated headers? UTF-8 BOM so umlauts don't mojibake in Excel?
      locale-formatted dates/numbers inside?) — the Export *menu* was verified, no file was ever opened
- [ ] **G7** timezone — 0 of 19 modules
- [ ] **G8** error states — no 4xx/5xx/offline/permission-denied message ever triggered and read
- [ ] **G9** sorting / collation (umlaut order: ä/ö/ü)
- [ ] **G10** search with diacritics ("Ernährung")
- [ ] **G19** a11y depth (focus order, SR announcement language, `aria-live` toast language, alt text)
- [ ] **G20** email templates previewed per-language (SCE#1 still unresolved; 9 Preview-Emails types never
      opened per language)
- [ ] **G21** number grouping with large values (`1.234.567`) — needs seeded data
- [ ] **G22** print / PDF output
- [ ] **G23** concurrent tabs + locale precedence (browser `Accept-Language` vs account preference)

**Tier 4 — verification debt:**
- [ ] **G18** — `Regression_Report.md` is still empty. 37+ bugs, **zero** re-verifications.
- [ ] **G24** — test-data cleanup: challenge 25441, Content-Library item "Managing Workplace Stress…",
      employee "QA Test Account", +1 point granted to a real user. None UI-deletable.
- [ ] **G25** — Health Insights: the iframe now renders (670×692), so the old "refused to connect" blocker
      note looks **stale** — re-confirm, and get product to rule it in or out of scope.

### ◐ Partially done for German
- **G2 visual screenshot review** — I reviewed *my own* new screenshots (which is how OV#8/#9, SET#3, EV#4,
  CC#6 were caught). The **original 79 screenshots from the 2026-07-21/22 pass have still never been
  visually re-reviewed.**
- **G11 responsive** — 1024 / 1366 / 1440 now covered. **768 and 375 not tested.**

### Loose ends from Run 5–6 specifically
- [ ] **Create Content picker modal** never opened (`?action=create` doesn't auto-open it) — CRC#1/#2 not
      re-checked for layout.
- [ ] **5 of 6 report pages** never measured (only Employee Report; they share table/filter components).
- [ ] **Re-run the sweep with the corrected `vis()` helper** if exact overflow *pattern counts* matter for
      triage — the old helper didn't reject elements collapsed by an ancestor, so counts may be inflated.
      All headline findings were screenshot-verified and are unaffected.
- [ ] **Re-run the UI sweep on the Email Designer modal once ED#1 is fixed** — it shows 0 overflow today
      only because none of it is translated yet; localising it will introduce text-expansion risk.

### Suggested finishing order for German
`G1 (30m) → G3 (10m) → G5 + G6 (1.5h, the P1 leads) → G13 (1h) → G4 (30m) → G8 + G7 (1.5h)`

---

## SPANISH (es) — status as of 2026-07-28 (Runs 7 + 8)

Spanish is now the **second-deepest language** on the dashboard: 18 of 19 modules covered for layout **and**
strings. Detail: `bug-logs/ui-break-sweep-es.md` (Run 7) + `bug-logs/spanish-full-sweep.md` (Run 8).

### ✅ Done
| Dimension | Coverage |
|---|---|
| Layout / UI breaks | **18 of 19 modules** @1024; 5 of them also @1440 with en/de controls |
| Translation / string rendering (**G14 for es**) | **18 of 19 modules**, dictionary-verified leak detection |
| Cross-language comparison | 3-way en/es/de on the Run-7 findings → produced the OV#8a/OV#8b split |
| FE/BE classification | Applied — every leak proven [FE] wire-up via the es.json value check |

Findings: **ES#1** (P2, cold-load English filters, cross-module), ES#2, ES#3, ES#4, plus RPT#1 / CL#1 /
AE#1 / CC#1 confirmed reproducing in Spanish.

### ❌ Not done for Spanish
- [x] ~~Cold-load re-measurement of the 11 in-app-navigated modules~~ — **DONE 2026-07-28.** 1 of 11
      differed (Participant Report, +1 leak `Active Users`). All Spanish per-module counts are now
      cold-load-verified totals rather than floors.
- [ ] **Create Challenge builder steps 2–5** (duration / audience / tasks / review) — only step 1 measured.
- [ ] **G15 — Spanish dynamic flows** (validation, toasts, live submits). Zero coverage; German-only.
- [ ] Widths 1366 / 768 / 375.
- [ ] **G13 glossary / register pass for Spanish** — *usted* vs *tú* consistency across 19 modules never
      examined. **No browser needed**; the captured strings are already sufficient. **~1 hr.**
- [ ] **All Tier 1–4 items listed under German above also apply to Spanish** — and **G5 (comma-decimal
      input) is arguably more urgent for Spanish**, which also uses a comma decimal separator.
- Not measured by design: Create-Content picker + Email Designer modals (**no i18n keys exist**, so no
  language can localize them — deduction from the dictionary, not a measurement).

### ES#1 — scope resolved by measurement (was flagged as engagement-wide; it is not)
Cold load vs in-app navigation give **different languages on the same route** — but after re-measuring all
11 affected modules on cold loads, **only 1 differed.** ES#1 is confined to three components (Content
Library type filters, Wellness Leagues filter chips, Participant Report `Active Users`), so it does **not**
qualify every ✅ in this engagement and does **not** invalidate the German pass.

Still worth knowing: it is a genuine **P2** because the cold state is the state real users land in, and it
is a **different defect from RPT#1** despite looking identical on screen — RPT#1 stays English in both
states (hardcoded), ES#1 goes Spanish once the dictionary is warm (init-order race). Fixing one does not
fix the other. Table in `bug-logs/spanish-full-sweep.md`.

**Standing method rule regardless of scope:** verify localization by **direct URL**, not by clicking the
sidebar — in-app navigation can hide this class of bug. (Saved to memory.)

## FRENCH (fr) / PORTUGUESE (pt) — not started on the dashboard
Only dictionary parity (991/991, a *file* check) plus 3-of-19 module spot-checks exist for fr/es from the
original pass — see **G14/G15**. Portuguese has no dashboard coverage at all.
