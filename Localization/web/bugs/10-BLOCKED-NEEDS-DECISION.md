# 10 — BLOCKED & NEEDS DECISION

Items that **cannot be closed by QA alone.** Each has an explicit owner and what specifically would unblock it,
with an effort estimate where one is meaningful.

Gap IDs (`W1`–`W19`) refer to `../COVERAGE_ANALYSIS.md`.

---

# ═══ BLOCKED ON ACCESS ═══

## US / Europe / E2E servers — 0 of 5 modules · **the single biggest coverage gap** · W17

Everything in this entire report is the **India tenant**.

**Why it matters specifically here:** locale formatting, currency and timezone are exactly the dimensions that
vary per server — and they are already this report's weakest area (`04-LOCALE-FORMATTING.md`), with **currency
never verified at all** and **timezone at 0 of 5 modules**.

**Unblock:** credentials / access for the other servers.
**A full re-run is NOT needed.** The formatting-sensitive surfaces in 2–3 languages per server would cover most
of the risk — roughly a day, not a week.

## Timezone — 0 of 5 modules · W16

Never tested in any language. Needs an account configured to a **non-IST** timezone to be meaningful at all —
with everything on IST there is nothing to compare against.

## Currency — never verified

No currency-bearing screen was reached on this surface. The dashboard found `$0` rendering on an India tenant
in a German session; **whether the same is true here is unknown.**

---

# ═══ BLOCKED ON A DEV-SIDE ANSWER ═══

## B25 root cause — runtime language desync · W6 · **highest-value open question**

**Observed and reproduced** (4 consecutive fresh loads, 3 modules), **not explained.** It corrupts backend
content queries, not just chrome.

**B39 explains part of it** — the asymmetry where content changes with language and chrome never does *is* the
signature of hardcoded chrome. **What remains unexplained is why the content query language drifts.**

**Unblock:** dev access to the language-state code. **Do not close B25 when B39 is fixed.**

**Consequence while it stays open:** every ✅ in this report is **point-in-time only**. That caveat cannot be
removed by more testing — only by a root cause.

## B39 confirmation — is the finding correct? · **please try to refute this**

B39 rests on **static analysis plus rendered-output correlation, not a source review.** Six independent methods
agree, but a developer with repo access can confirm or refute it in minutes.

**We would rather be corrected than believed.** Specifically:

- Is there a Fit chunk we did not load that **does** use the translation service?
- Are the 5 probe strings we could not locate (`Active Minutes`, `Wellness Score`, `Featured Content`,
  `Weekly Rank`, `Avg Sleep`) in an unloaded chunk, or do they come from the API?
- Was Fit i18n ever scoped, and is `/ng/assets/i18n/fit/` a path that was *planned* rather than *built*?

## B32 source — end date before start date

`07 Oct 2025 - 15 Sep 2025`. Either **bad stored data** or a **start/end transposition in rendering.**
Different fixes, different owners. **Needs a call before assignment.**

---

# ═══ BLOCKED ON BLAST RADIUS ═══

## Write flows never reached · W11 · **the highest-value functional gap remaining**

**Now reached:** the **Log Activity** submit path — completed 2026-07-30, see W13 below.

**Still unreached:** Challenges "+Add", Community create-event, Community add-post.

**Why it matters:** write flows are where **validation, toasts and dialogs** all live — three dimensions this
report can only partially assess without them.

**Why they were deferred — and this was a legitimate call, not laziness:** these write to a real account, and
**there is no delete control**, so anything created is permanent debt. The dashboard engagement has four items
of exactly that kind of debt, one of which is **now visible to real employees** (see `08-ENHANCEMENTS.md`).

**Unblock — needs a decision, not just time:**

| Option | Cost |
|---|---|
| Accept the debt: submit one activity, capture the toast, document the record as permanent | ~15 min, 1 permanent record |
| Get a disposable test account | clean, needs provisioning |
| Get a delete path | fixes the underlying problem too |

## ~~Toast localization on a successful write~~ · W13 · **CLOSED 2026-07-30**

**Closed by completing one real write**, with the user's explicit authorisation. Debt recorded in
`../TEST_DATA_DEBT.md` **before** the action; the record is **confirmed not deletable** and stands disclosed.

**Result: there is no success toast at all.** `POST /activity/save` → 200, record created, modal closed,
**0 toasts** with the observer installed before the modal opened and a 2.5 s wait.

**So this gap cannot be a localization defect** — the feature is absent, not untranslated. It folded into a
**widened B31**: success and failure are indistinguishable to the user.

---

# ═══ NOT TESTABLE AS SPECIFIED ═══

## Dictionary completeness — permanently unassertable for Fit · W4

The dashboard's single strongest analytical claim — *"18 dictionaries × 991 keys, 0 missing → therefore every
defect is a wire-up gap, not a missing translation"* — **eliminated an entire defect class in one sentence.**

**That reasoning is structurally unavailable here.** Not "blocked pending a B33 fix": **there is no Fit
dictionary to complete** (B39). Any statement of the form "N% of Fit strings are translated" is unprovable on
this surface, and should not be attempted.

## Accented input in search — N/A, the premise was wrong · W10

`/ng/fit/programs` has **zero `<input>` elements.** There is no search box on Fit web. The gap was **inherited
from the dashboard**, which does have search and does fail this test.

**Remaining to confirm:** that no search exists on Community or Challenges either, then mark N/A permanently.
**~5 min.**

## Validation messages — not reachable · W9

The Hiking form's submit is **not** gated (`disabled:false`, `aria-disabled:null`), but **every field ships a
valid default**, so no invalid state can be reached without defeating the custom controls.

**So no validation message could be found to check for translation.** This is different from "validation is
untranslated" and different from "validation passes" — it is **unreachable**, and recorded that way.

## Language axis — re-scoped, not open · W12

Testing the 11 untested languages **would no longer change the frontend verdict**: hardcoded literals cannot
translate in any language, including the 5 already tested. B39 gives every language a **derived** answer.

**What further language passes would still measure:** **backend** translation coverage per language — which is a
`11-BACKEND.md` question, and where **Arabic already has none at all**.

## Mobile widths — never measured

768 and 375 were not measured at any point. Desktop widths (1024–1920) are fully covered.

---

# ═══ NEEDS A PRODUCT / DESIGN DECISION ═══

| # | Decision | Context | Owner |
|---|---|---|---|
| **1** | **Is Fit web localization in scope at all, and at what priority?** | B39 means this is **a project, not a bug fix**. Every other frontend string finding in this report is downstream of this one answer. **The most consequential decision here** | Product |
| **2** | **Is `Wellness Score` a brand term?** | B9. If yes → closed, not a bug. If no → a real bug **and** part of B39's externalisation scope. **The same term appears on the admin dashboard — decide once for both** | Product |
| **3** | **One register per market** | B12 — formal/informal mixed in de, es, fr on the **same 3 structural positions**, suggesting one shared source string. The dashboard has the identical problem. **One decision should cover both surfaces** | Product / Content |
| **4** | **One glossary per language** | B21 (es `Retos` vs `Desafío`) and the dashboard's German `Herausforderung` vs `Challenge`. **Decide once for both surfaces** | Content |
| **5** | **Should the language picker use endonyms?** | B34. Current behaviour is **defensible**; endonyms are the stronger convention. **Not a bug either way** | Design |
| **6** | **Should admins be able to delete library content?** | No delete control exists, which is why test data accumulates — **and is visible to employees**. Same finding as the dashboard's DEL#1 | Product |
| **7** | **Which units for which markets?** | BE-7 / B6 / B18 — units arrive **English and imperial**, pre-formatted, for metric locales. Needs a rule before the backend can be fixed | Product |
| **8** | **Is Arabic a supported market?** | RTL works here (better than the dashboard) but **Arabic has zero backend translation coverage**. Shipping it half-supported is a business call | Product |

---

# ═══ NOT DONE — PROCESS ═══

## Nothing has been filed to Jira · W19

**40 frontend bugs and 24 backend findings, none in front of a developer.** The dashboard's pipeline
(12 category files → 13 tickets grouped by **fix unit**) has not been run here.

**When it is run, the grouping should differ from the dashboard's**, because the defect shape differs:
`01-P1-P2-CRITICAL.md` is already ordered by leverage and that order is the recommended ticket order. Most of
Tier 6 there should be **one ticket referencing B39**, not eleven separate tickets — filing them individually
would misrepresent both the effort and the fix.

## No regression pass · W18

**40 bugs, 0 re-verified.** Same gap the dashboard has.

**When run, it must sample late in a long session** — otherwise B25 will produce false passes.

## French and Portuguese evidence is degraded · W5

Both passes ran **entirely inside the B25 English-fallback state.** Their recurrence confirmations rest on
structural-position matching rather than clean observation, and one B14 data point was **discarded as
confounded**.

**Impact is narrow:** it affects *confidence in recurrence*, not categorisation. But **"4 languages tested"
should not be read as 4 clean passes** — it is closer to 3 (de, es, ar) plus 2 degraded.
</content>
