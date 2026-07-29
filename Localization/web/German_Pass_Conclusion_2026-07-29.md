# German Pass — 2026-07-29 (re-run) — quantified regression

**Session:** healthy backend (page served real data; the earlier 502 outage had ended), `<html lang>="de"`,
profile saved as German, re-authenticated after the outage killed the session. Width 1440.
**Method:** all 6 routes, fresh load each, load-guarded (chrome-presence, not fixed timeout), German-string
ratio + overflow detection per route.

## The headline number

| Module | German | Total strings | **% German** | Previously documented as |
|---|---:|---:|---:|---|
| **Programs** | 0 | 67 | **0 %** | "FE chrome localizes well in German" |
| **Diary** | 2 | 72 | **3 %** | **"the best-localized screen in the whole engagement"** |
| **Trends** | 2 | 41 | **5 %** | partial (B19) |
| **Community** | 4 | 43 | **9 %** | 0 % — B16, already known |
| **Summary** | 11 | 69 | **16 %** | "strong localization, nav/headings/labels translated" |
| **Challenges** | 12 | 60 | **20 %** | "strong localization" |

**Overall ≈ 9 % of visible strings render German.** Every module except Community has **regressed**, and the
two worst are the two that were previously rated best.

## What this proves

1. **B33 is confirmed with a healthy backend.** The `/ng/assets/i18n/fit/de.json` path still returns
   `text/html`, 115,655 bytes, identical to the English one, while the sibling global dictionary serves valid
   JSON. The 502 outage was **not** the cause.
2. **The regression is real and severe.** Diary at 3 % and Programs at 0 % cannot be reconciled with the
   2026-07-24/28 records unless the Fit dictionary was being served then and is not now.
3. **Community (B16) was never special.** It was simply the first module observed in this state. Its
   "0 % own chrome, shared widgets still localized" signature is now the signature of *every* module.
4. **The "module quality does not transfer between languages" conclusion is in doubt.** B14 (de-only) and
   B20 (es-only) were measured on different days. If the dictionary broke between those measurements, the
   asymmetry may be a **timing artifact, not a language property.** Re-derive after B33 is fixed.

## What still renders German — and why it matters

The surviving strings are consistent across modules: `Schritte`, `Aktive Minuten`,
`Durchschnittliche Schritte`, `Achtsamkeitsminuten`, `Durchschnittlicher Schlaf`, `Wöchentlicher Rang`,
`Wöchentlicher Fortschritt`, `Ihr neuestes Abzeichen`, `Hämoglobin`, `Aktualisiert am …`,
`Es gibt keinen Beitrag`, `Nächster Meilenstein`, `Gesamtrang`, `Meilenstein-Fortschritt`,
`E-Marathon-Herausforderung (endet in 22 Tagen)`, `Renn-Herausforderung (endet in 2 Tagen)`.

Most of these are **not in the global dictionary** (checked by value). So they are not "global-dictionary
residue" as first assumed.

**Hypothesis (NOT verified — stated as a hypothesis):** two string-delivery mechanisms exist — some strings
build-time inlined into the Fit JS bundle (still working), others runtime-loaded from the dictionary (broken
by B33). The clean split — metric/card labels work, section headings and CTAs don't — fits this. **Verifying
it requires a dev or a bundle search; do not report it as fact.**

## Confirmed in German this run

| Bug | Evidence |
|---|---|
| **B1** | `Aktualisiert am 14 Jul 2025`, `Aktualisiert am 01 Apr 2026` — German prefix, English date |
| **B3 (widened)** | **All four** nav tabs English on every route, not just "Challenges" |
| **B4** | `Week 1` English inside cards whose other labels are German |
| **B6** | `2 hrs 54 mins`, `8 mins`, `0 sec`, `/day` |
| **B7** | Weekday axis `T F S S M T W` |
| **B12** | `Ihr neuestes Abzeichen` — formal *Ihr* against the informal house voice |
| **B16** | Community 9 %; only shared widgets localized — reproduces exactly |
| **B19** | Trends `Week/Month/Year`, `Steps Overview`, `Activity Details`, `Steps Covered` all English |
| **B23** | Programs: 33 images, 1 broken, **4 double-extension**, 23 console errors — **not fixed** |
| **B29** | `.ch-slide` +36px, 10 cards on Challenges, 1 on Summary, 1 on Community — **identical to English → language-independent confirmed** |
| **B33** | Fit dictionary still `text/html` with a healthy backend |

## New regression detail

**Challenges subtitle regressed.** Now renders English *"Compete with peers & colleagues, track your
tasks."*; the 2026-07-24 German pass recorded *"Tritt gegen Kollegen an und verfolge deine Aufgaben."*
A concrete, quotable before/after for the B33 report.

## Layout in German — no new breaks

Overflow counts are **identical to the English baseline** at 1440: Challenges 10 × 36px, Summary 1 × 36px,
Community 1 × 36px, Diary 0, Programs 1 × 10px (`.cc-title`, a content title that is a raw URL), Trends 0
structural. **No German-specific layout defect** — unsurprising, since so little German text is rendering to
expand anything. **Text-expansion layout testing is impossible until B33 is fixed.**

## Not done, and why

- **French pass** — not run. With the dictionary unserved it would return the same ~0–20 % English-fallback
  numbers and produce no French-specific information. The one exception worth doing is the **de→fr switch**,
  to capture B2's `{language}` failure case (only appears when switching *from* a non-English session).
- **Functional deep dive per module in German** — the functional baseline (B30/B31/B32, sub-tab switching,
  unit toggle, modal semantics) was established in English earlier today and is language-independent. Re-running
  it in German would re-confirm B33, not test function.
- 1024/1366 widths · weight-edit flow · mood-modal semantics · contrast · keyboard traversal.
- **Provisional until B33 is fixed:** any claim about per-module or per-language localization quality on this
  surface.

## Recommendation

**Fix B33 first, then re-run this exact table.** It is now a 6-number regression test: if the percentages do
not rise sharply, the fix did not work. If they do, re-derive B3/B14/B16/B19/B20/B25 from scratch — several
may disappear or change scope.
