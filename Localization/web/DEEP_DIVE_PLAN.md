# Employee Fit Web — deep-dive plan to a categorised bug folder

**Written 2026-07-30.** Goal: bring the employee-web engagement to the same depth the admin dashboard
reached, then produce `bugs/00-INDEX.md` + `01`–`11` — a categorised report organised by **bug type**.

**Starting point:** 38 bugs (`bugs/bug-log.md`, B1–B38) · 5/5 modules · 5/16 languages · 4/4 widths ·
0 of 12 category files · 0 filed to Jira. Gap register: `COVERAGE_ANALYSIS.md` W1–W19.

---

## Why testing comes before the report

The dashboard engagement categorised first and had to regenerate: the overflow detector was wrong, so
"Truncation ✅ none seen" was recorded for nearly every module and later overturned. The rule learned there:
**close the gaps that could change a bug's category or severity first, then categorise once.**

Applied here, only some of W1–W19 can change the report. Those are the phases below. The rest
(servers W17, timezone W16, B25 root cause W6, dictionary completeness W4) either need access we don't have
or a dev-side answer, and get recorded as known gaps rather than blocking the report.

---

## Phase 1 — The language axis (W12) · ✅ DONE 2026-07-30 — answered by B39, see run log

**The open question with a plausible P2 answer:** the app fetches Fit dictionaries for only **7 locales**
(en, de, fr, es, pt, pt-BR, pt-PT) but the profile offers **16 languages**. So what happens when a user picks
**Korean, Japanese, Russian, Chinese, Italian, Dutch, Vietnamese, Hungarian, Polish**?

Two candidate answers, very different severities:
- Fit falls back to English **silently** while the rest of the product translates → **P2**, and it affects
  9 of 16 shipped languages
- Fit refuses the selection / shows raw keys → worse

**Method — one switch, not nine.** A language change forces a full logout + re-login, so batching matters.
Pick **one non-Latin language with no Fit dictionary** (Japanese or Korean) and sweep all 5 modules. That
single pass answers the core question. Then confirm the *pattern* cheaply by fetching the sibling
`/ng/assets/i18n/<lang>.json` for every offered locale — a dictionary inventory needs no login at all.

**Also closes in the same pass:**
- **W14** — CJK glyph rendering (no non-Latin script except Arabic has ever been rendered here)
- The **B33 control** re-run: does the perks app translate while Fit doesn't, in a *third* language?

**Deliverable:** per-language dictionary inventory table + a module sweep. New bug IDs from **B39**.

---

## Phase 2 — Three untested functional dimensions (W9, W10, W13) · ◐ PARTIAL 2026-07-30 — see run log

All three are interaction tests that run in whatever session Phase 1 leaves behind.

| Gap | What has never been tested | Why it matters |
|---|---|---|
| **W9 — F3 validation** | Validation gating **and** whether validation messages are translated. Never tested in any language. | The dashboard's *first* error-state string captured was hardcoded English (UP#6). Error states are the least-localized layer in most products. |
| **W10 — F6 accented input** | Search with accented characters. | The dashboard **failed** this — search folds case but not diacritics. Likely shared code, so there is a strong prior. |
| **W13 — F4 toasts** | Toast capture was run **without the required ~2 s wait**, so every "no toast" result on this surface is unconfirmed, not negative. | Half of F4 is currently unproven either way. |

**Method notes carried from the dashboard:** install the MutationObserver **before** the action and wait
~2 s; if observer timing is uncertain, record **inconclusive** rather than asserting a defect.

---

## Phase 3 — Write flows (W11) · blast-radius controlled

Never reached: Challenges "+Add", Community create-event, Community add-post, and the loggable-activity
modals' full submit paths. Deferred earlier for blast radius — legitimate, but still a hole, and write flows
are where validation, toasts and dialogs all live.

**Blast-radius rules (from `CLAUDE.md`):** UAT tenant · outward-facing content targets self only · formal
content names, never "test/delete" junk · note anything not UI-deletable before creating it · revert what
can be reverted.

---

## Phase 4 — Regression sample (W18) + the B25 point-in-time check (W6)

38 bugs logged, **zero re-verified.** Re-check a sample late in a long session. This does double duty: it
tests whether any bug has been fixed, **and** it directly probes B25 (runtime language desync), because a
module that passed early and reads English later *is* B25 reproducing.

Until B25 is explained, every ✅ in this engagement is **point-in-time only** — that caveat goes in the
report regardless of what this phase finds.

---

## Phase 5 — Build the categorised bug folder

Mirror the dashboard's structure so the two engagements are directly comparable, each file split
**frontend on top / backend at the bottom**:

| File | Contents |
|---|---|
| `00-INDEX.md` | Read first. FE/BE totals, per-file guide, language + module coverage, checklist status |
| `01-P1-P2-CRITICAL.md` | Fix-first view, ordered by leverage. **The only file allowed to repeat a bug** |
| `02-UNTRANSLATED.md` | English strings on a localized screen |
| `03-UI-LAYOUT.md` | Truncation, clipping, spill, overlap, **RTL/bidi (B35)** |
| `04-LOCALE-FORMATTING.md` | Date / time / number / separator / currency / units |
| `05-LINGUISTIC-QUALITY.md` | Register/tone, terminology, casing, coherence |
| `06-FUNCTIONAL.md` | Interaction, validation, CRUD, dialogs, persistence |
| `07-ACCESSIBILITY.md` | `<html lang>`, labels, contrast (B37), custom controls (B36) |
| `08-ENHANCEMENTS.md` | Polish / parity — not defects |
| `09-NOT-A-BUG.md` | Investigated and ruled out, with the reason |
| `10-BLOCKED-NEEDS-DECISION.md` | Blocked flows + open product questions |
| `11-BACKEND.md` | Replaces the dashboard's `11-AC3-FALLBACK.md` — this surface **has** confirmed backend defects (the dashboard had none), so backend gets its own file. Derived from `BACKEND-BUGS.md` |

**Authority:** `bugs/bug-log.md` stays the source of record; `00`–`11` are a derived view. If they disagree,
the log wins and the report is regenerated.

**Existing `FRONTEND-BUGS.md` / `BACKEND-BUGS.md`** become inputs to this structure, not siblings of it —
`BACKEND-BUGS.md` maps onto `11`, `FRONTEND-BUGS.md`'s tiers map onto `01`.

---

## Explicitly NOT in this plan — recorded as known gaps

| Gap | Why not now |
|---|---|
| **W17** — US / Europe / E2E servers | No access. Biggest single coverage gap; needs credentials. |
| **W16** — timezone | Needs an account on a non-IST timezone. |
| **W6** — B25 root cause | Needs dev access to the language-state code. Observed and reproduced, not explained. |
| **W4** — dictionary completeness | Blocked by **B33** — the Fit JSON path returns the SPA shell, so no key-parity claim is possible on this surface. This is why the dashboard's strongest argument ("991 keys, 0 missing → every gap is wire-up") is **unavailable here**. |
| **W5** — fr/pt evidence degraded | Both passes ran inside the B25 fallback state. A clean re-run is worth doing but changes recurrence confidence, not categorisation. |

---

## Run log

| Date | Phase | Outcome |
|---|---|---|
| 2026-07-30 | Plan written | — |
| 2026-07-30 | **Phase 1 — language axis (W12)** | **Root cause found: B39.** The Fit module has **no i18n mechanism** — 0 of the app's 79 translation calls are in the Fit chunk (the largest bundle in the app); strings are compiled as static template literals. Proven 6 ways. **Supersedes B33's framing**, which implied a cheap serving fix; `B33_DEVELOPER_ISSUE.md` corrected accordingly. Also: `/ng/assets/i18n/fit/` is the SPA catch-all (returns identical 115,655 bytes for `zzz.json`), the app never requests a Fit dictionary, and 0 of 12 live Fit strings exist in any dictionary. **W12 re-scoped, not closed** — every language now has a derived answer. Adjacent (out of Fit scope): `pt-BR`/`pt-PT`/`zh` have no dictionary file; `en`/`pl`/`hi` are a dictionary generation behind (~30% fewer keys). |
</content>
</invoke>
| 2026-07-30 | **Phase 2 — validation / units / toasts** | **0 new bug IDs** — a good result. **PASS:** Log Activity km→mi converts correctly (5.0→3.1), which *strengthens* B28 by proving the Log Water miss is local to one modal, not a platform gap. **4 confirmations** (B1 via `Thursday, 30 July 2026` + `2:33 PM` + `5.0`; B8; B23 via 20 console errors; B39). **3 reach extensions** — B30 now on **3 modals** (all `role:null`, focus stays on `BODY`), B36 on a 4th surface (Hiking form has **zero** native inputs), B39 across Quick Add (12/12 English), the activity picker and the Hiking form. **1 false positive caught by opening the screenshot** (`Strength/Weight Training6` is a correctly-spaced count badge). **W9 partial** — submit is not gated but all fields ship valid defaults, so no validation message is reachable to test. **W10 N/A — premise wrong:** Fit web has no search input; the gap was inherited from the dashboard. **W13 still open** — needs a real submit, deferred on blast radius. Also: dashboard test-data debt is **visible to employees** in the Programs library. |
