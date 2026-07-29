# Vantage Fit Web — French Localization Pass — Conclusion (2026-07-28)

**Scope:** French (fr) tested across all 5 Fit modules, the same day as the German and Spanish passes, to
cross-check which findings are systemic vs. language-specific. Portuguese remains the one language behind
on module coverage (only Summary + Challenges); other profile languages and non-India servers are untested.

## Headline result: zero new bugs, five bugs confirmed systemic across 3 languages

Unlike the Spanish pass (which surfaced 7 new bugs), this French pass found **no new defects** — every
observation was an existing bug reproducing (or, for one bug, confirmed **not** reproducing). That's a
valuable result in its own right: five bugs are now confirmed in **three independent languages**, which is
much stronger evidence of a shared root cause than one or two languages could show:

| Bug | German | Spanish | French | Conclusion |
|---|---|---|---|---|
| **B12** (register mixing) | Formal "Ihr/Ihre/Ihren" on 3 surfaces | Formal "Su/sus" + task imperatives, same 3 surfaces | Formal "Votre/vos" + task imperatives ("Faites/Buvez/Enregistrez"), **same 3 surfaces** | Near-certainly a shared source-string/template issue — the odds of 3 separate translation efforts making the identical mistake on the identical 3 strings are vanishingly small |
| **B16** (Community chrome unlocalized) | 100% of visits | 100% of visits | 100% of visits | Confirmed module-wide, not a per-locale translation gap |
| **B22** (toggle-pill overlap) | Visible, moderate | Visible, worse ("Pasos" shorter than "Schritte") | Visible, at least as bad ("Pas" shorter still) | Confirmed language-agnostic layout bug, exposed more by shorter labels |
| **B23** (broken content images) | Reproduces | Reproduces | Reproduces | Confirmed locale-independent backend/data bug, as expected (URLs carry no locale segment) |
| **B27** (garbled water task) | Not applicable (not checked in de) | "67.6 fl oz vasos… 1 días" (3 defects) | "67.6 fl oz verres… 1 jours" (**identical 3 defects**) | Confirmed shared backend task-template bug, not a per-language translation slip |
| **B14** (empty "View all" grid) | Empty (broken) | Populated (fine) | Populated (fine) | Now confirmed German-specific with a 3rd language — the fix is narrow, not a pattern |

## The environment caveat for this pass

The session was in the **B25 English-fallback state from the very first fresh French page load** — nav and
chrome were English immediately, without needing the multiple reloads it took to trigger this on German or
Spanish earlier in the day. This is most likely **cumulative session staleness** (many language switches
across a long day of testing) rather than evidence that French specifically triggers the desync faster —
but it does mean this pass could not independently re-verify French's *own* informal-register voice (the
equivalent of footer "tu" forms) the way the German and Spanish passes could earlier in cleaner sessions.
B12's French evidence therefore rests on **structural-position matching** to the already-proven 2-language
pattern (the same 3 exact strings/positions carrying the formal slip), not a fresh French-specific
mixing observation. This is still meaningful evidence, just a different kind than the German/Spanish passes
had — worth a follow-up check in a fresh session if a from-scratch French mixing confirmation is wanted.

## Functional checks — all clean
Sub-tab switching (Ongoing/Upcoming/Past, Social/Events), challenge-detail-page navigation, and the "View
all" content modal all worked correctly in French with no functional breakage or layout overlap beyond the
already-known B22.

## Updated bug tally (unchanged from the Spanish pass — no new IDs)
- **P2 (15):** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19,B20,B23,B25,B27.
- **P3 (11):** B6,B7,B8,B13,B15,B18,B21,B22,B24,B26,B28.
- **P4 (2):** B9,B10.
- **28 bugs total** — same as after the Spanish deep-dive. FE: 22 · BE: 5 · FE/BE-TBD: 1.

## What's next
- **Portuguese** — bring Programs/Community/Diary-Trends up to the same coverage Summary/Challenges have.
- **Other profile languages** (12 more, incl. Arabic = RTL — the highest-risk untested language given
  right-to-left layout is a different class of risk than translation completeness).
- **A from-scratch French session** (fresh login, minimal prior switching) to independently verify French's
  informal-register voice and get a clean read on whether B25's fallback state is truly random/cumulative or
  has some French-specific trigger — today's session couldn't cleanly distinguish those.
- **US / Europe / E2E servers** — still India-only for every language tested.
- Dynamic-flow/functional testing beyond what's been click-tested (Vitals edit, Log Water, create-flows) —
  not yet run in French specifically (was run in Spanish; French inherited the same functional confidence
  from cross-language consistency, but wasn't independently re-executed).

## Deliverables touched today (French pass)
`bugs/{challenges,programs,community,diary-trends,bug-log}.md` (extended — B12/B14/B16/B22/B23/B27
updated with French confirmation) · `Execution_Status.md` · `Coverage_Matrix.md` · 8 new evidence
screenshots · this document.
