# Vantage Fit Web — Portuguese Localization Pass — Conclusion (2026-07-28)

**Scope:** Portuguese (pt) tested across all 5 Fit modules — the fourth language this engagement has run
through all 5 modules (after German, Spanish, French). **This closes out module coverage for every
language tested so far**: German, Spanish, French, and Portuguese now each have a pass across Summary,
Challenges, Programs, Community, and Diary/Trends. The remaining language gap is entirely the other 12
profile languages (Arabic RTL, pt-BR/pt-PT variants, etc.) and non-India servers.

## Headline result: 4-for-4 confirmation on four bugs, and a lesson in not over-generalizing

Like French, this pass found **zero new bugs**. Four bugs are now confirmed identically in **all 4**
languages tested:

| Bug | de | es | fr | pt | Conclusion |
|---|---|---|---|---|---|
| **B16** (Community chrome unlocalized) | ✅ fails | ✅ fails | ✅ fails | ✅ fails | Confirmed module-wide across every language tested — highest-confidence systemic bug in the whole engagement |
| **B22** (toggle-pill overlap) | ✅ visible | ✅ worse | ✅ worse | ✅ same | Confirmed language-agnostic layout bug |
| **B23** (broken content images) | ✅ | ✅ | ✅ | ✅ | Confirmed locale-independent backend/data bug, exactly as the URL structure predicted |
| **B27** (garbled water task) | *(not checked in de)* | ✅ "67.6 fl oz vasos… 1 días" | ✅ "67.6 fl oz verres… 1 jours" | ✅ "67.6 fl oz copos… 1 dias" | 3-for-3 identical 3-defect pattern — about as strong as evidence gets for one shared backend template |

But this pass also produced two results that are valuable precisely because they **didn't** just confirm
the pattern by rote — a reminder that "test the same thing in another language" needs judgment, not a
checklist run on autopilot.

### 1. B12 (register mixing) does NOT clearly apply to Portuguese — and that's a real finding, not a gap
German (*Sie/Ihr* vs *du*), Spanish (*usted/su* vs *tú*), and French (*vous/votre* vs *tu*) all have a clear,
actively-used informal register that contrasts with the formal forms found in the 3 known bug surfaces.
Portuguese's "**seu/sua**" (found in the same 2 surfaces: "A sua medalha mais recente", "suas necessidades
abrangentes de bem-estar") and "**você**"-conjugated imperatives ("Caminhe/Beba/Registre") are **not** a
marked formal register the way the others are — they're the everyday default in most Portuguese, especially
Brazilian Portuguese, where "tu" is comparatively rare. No "teu/tua" or "tu"-conjugated form was found
anywhere in the app to contrast against. Without a competing informal form in actual use, there's nothing to
call "mixed." **This is logged as checked-and-inconclusive, not as a 4th confirmed language** — forcing the
pattern on just because the surface strings look similar to the other three would have been wrong.

### 2. B14's Portuguese retest was confounded by B25 — the result had to be discarded, not counted
Programs' "View all" grid returned 0 items in Portuguese, which on the surface looks like B14 recurring
outside German. But the retest happened while the session was confirmed in the **B25** English-fallback
state (the Library carousel was serving the full English-baseline content set at the same moment) — and this
endpoint has no visible per-request locale parameter, so the backend must be resolving language from
session state that may not have actually been "Portuguese" at call-time. **This result can't be attributed
to Portuguese and doesn't change B14's German-specific conclusion** (which rests on 2 clean Spanish/French
re-tests) — but it also can't be waved away; a genuinely clean Portuguese re-test is still needed to be sure.

## Functional checks — all clean
Sub-tab switching, challenge-detail-page navigation, and the "View all" content modal all worked correctly
in Portuguese, with no functional breakage or layout overlap beyond the already-known B22.

## Minor copy note
Community's empty-state text has a small typo: "**Não há postagem..**" (doubled period). Not logged as a
numbered bug — noting for content-team awareness alongside the similar "Este desafio tem Encerrado" grammar
oddity (now confirmed in 3 languages: es "Finalizado", fr "Terminé", pt "Encerrado").

## Bug tally (unchanged — no new IDs from this pass either)
- **P2 (15):** B1,B2,B3,B4,B5,B11,B12,B14,B16,B17,B19,B20,B23,B25,B27.
- **P3 (11):** B6,B7,B8,B13,B15,B18,B21,B22,B24,B26,B28.
- **P4 (2):** B9,B10.
- **28 bugs total** across the whole engagement. FE: 22 · BE: 5 · FE/BE-TBD: 1.

## What's next
- **Other profile languages** (12 more offered: Chinese Simplified, Dutch, French Canada, Italian, Korean,
  Russian, Vietnamese, Arabic = RTL, Hungarian, Polish, Japanese, plus pt-BR/pt-PT variants distinct from the
  generic "Portuguese" tested here). **Arabic remains the highest-risk untested language** given RTL is a
  different class of layout risk than translation completeness.
- **A clean re-test of B14 in Portuguese** (and ideally English) — today's result was discarded as
  confounded, not resolved.
- **US / Europe / E2E servers** — still India-only for every language tested.
- **Root-cause investigation for B25** (the runtime language desync) remains the single highest-value open
  item — it's now been observed affecting Summary, Programs, Community, Diary, and Trends, across 3 of the 4
  languages tested, and it's the reason several other results this pass had to be qualified or discarded.
- Dynamic-flow/functional testing beyond click-testing (Vitals edit, Log Water, create-flows) — not
  independently re-run in Portuguese; inherits the functional confidence already established in Spanish.

## Deliverables touched today (Portuguese pass)
`bugs/{challenges,programs,community,diary-trends,bug-log}.md` (extended — B12/B14/B16/B22/B23/B27
updated with Portuguese confirmation or caveat) · `Execution_Status.md` · `Coverage_Matrix.md` · 8 new
evidence screenshots · this document.
