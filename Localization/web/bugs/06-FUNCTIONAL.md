# 06 — FUNCTIONAL / BEHAVIOURAL

Interaction, language persistence, silent failures, error and empty states, data correctness.

**Produced by driven interaction**, not DOM inspection: modals opened, unit toggles flipped, forms inspected,
toasts captured with an observer installed **before** the action and a wait after it.

**Blast-radius control applied throughout:** UAT account, no outward-facing sends. **One activity was submitted
deliberately on 2026-07-30**, with the user's explicit authorisation, to close gap W13 — the debt was recorded in
`../TEST_DATA_DEBT.md` **before** the write, and the record is **confirmed not deletable**.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`B25` (runtime language desync) · `B11` (preference not persisted) · `B31` (silent submit failure) ·
`B14` (empty content grid) — all **P2**.

---

## 🔴 B25 — [P2] Runtime language desyncs from `<html lang>` and the saved preference, mid-session · [FE]

> **⚠️ NEW LEAD 2026-07-30 — a candidate root cause.** The backend resolves language from the **account**, not
> from the request (see `11-BACKEND.md` / BE-1). The frontend resolves it **client-side**. **B11** says the
> preference **is not persisted** — so the client can hold language X while the account holds Y, giving
> **chrome in X and content in Y.** That is B25's exact signature, *including the part that had no explanation*:
> why the content query language drifts independently. **One cheap question closes or kills it: does the language
> selector write to the account, or only to the client?**

**No re-login. No language change.** The effective language observably drifts away from both `<html lang>` and
the stored profile preference.

- **Reproduced on 4 consecutive fresh loads**
- Confirmed on **Summary, Programs *and* Challenges** — not only the modules that first suggested it
- **It corrupts backend content queries, not just chrome:** Programs' Library served the full
  **English-baseline** content set instead of the Spanish-scoped set seen earlier the same day

### Why this is the most consequential functional bug in the report

**Every ✅ in this entire engagement is point-in-time only.** A screen that passes early in a session can read
English later. Any regression pass must re-check a sample **late** in a long session, or it will report false
passes.

It also means **B14 / B16 / B19 / B20 may be symptoms of one mechanism rather than four per-module gaps** —
Community looks like the deterministic case, the others intermittent.

### Partly explained by B39 — but only partly

B39 explains the **asymmetry**: content changes with language because it comes from the API; chrome never
changes because it is hardcoded. That was the desync's most visible signature.

**What B39 does not explain is why the *content* query language drifts** — but the BE-1 + B11 lead above now
offers a mechanism that does. Still needs dev confirmation. **Do not close B25 when B39 is fixed.**

### Practical rule for testers
**If a whole module suddenly reads English, suspect B25 before logging a new bug.** Confirm by loading a
known-good module in the same session.

---

## 🔴 B11 — [P2] Language preference not persisted — reverts to English after re-login · [FE/BE]

The user's language choice does not survive session expiry or re-login.

**Expect natural session expiry mid-run.** When it happens, **check what language comes back before doing
anything else** — that observation *is* this bug.

**Same defect on the admin dashboard** (logged there as F8#1, also P2, also the only item there needing backend
work). **One account-level persistence fix should serve both surfaces.** Filing two tickets invites two
half-fixes.

---

## 🔴 B31 — [P2] The app never confirms or denies a write · [FE] · **WIDENED 2026-07-30**

Originally logged for the **error** path (Log Water submit with no amount closes silently). A **real successful
write** was then completed and behaves **identically**:

| Outcome | What the user sees |
|---|---|
| `POST /activity/save` → **200**, record created and visible in Diary | dialog closes, **nothing said** |
| Submit invalid / fails | dialog closes, **nothing said** |

**Success and failure are indistinguishable to the user.** The correct framing is not "the error case lacks a
message" but **"there is no write confirmation at all."**

**The toast absence is confirmed, not assumed** — the observer was installed **before** the click and read
after a wait. This distinction matters: reading immediately after a click yields a **false** "no toast", and
that mistake produced one inconclusive result earlier in this engagement before the method was corrected.

**Same class as the admin dashboard's silent-failure family** (UP#4 — a detailed 400 discarded; SET#4 — an
out-of-range value blocked silently). On the dashboard the recommendation was to treat it as **one systemic
defect — "the app does not surface failed operations"** — rather than several tickets. **The same
recommendation applies here.**

**Follow-up worth doing:** check whether other Fit write flows (activity submit, sleep submit, community post)
discard failures the same way. If they do, one P2 multiplies across modules.

---

## 🔴 B14 — [P2] Health-bites "view all" opens an empty content grid · [FE/BE]

A locale-handling gap on a paginated endpoint: `GET /content/category/20` returns **empty** while
`POST /content/byCategoryName` **has content**.

**German-only — Spanish is fine.** This is the single clearest proof that **module quality does not transfer
between languages** on this surface, and it is why a German-only pass systematically misreports it.

**Caveat on record:** one B14 data point was discarded as **confounded by B25** (the desync had put the session
in an English-fallback state, so the content set could not be attributed). The bug stands on its other
observations, but its reach across languages is less certain than the others in this file.

---

## B24 — [P3] Offerings tab intermittently shows "Unable to load offerings right now" · [BE]

Transient **502** on `/marketplace/categories`. **Intermittent and environmental** rather than a code defect.

**Important context for anyone testing this surface:** four separate outages were observed across this
engagement, and one of them **invalidated a whole language pass** (a Chinese-equivalent run returned "no
findings" purely because page data never loaded — it was **discarded rather than recorded as clean**).

**A 401 from a bare `fetch` is NOT an outage signal** — the app supplies auth headers a plain fetch does not,
so 401 is expected. **Only 502 indicates a real outage.** This rule exists because a health check added
mid-engagement misfired immediately on exactly that confusion.

---

## B32 — [P3] A past challenge shows an end date before its start date · [FE-BE TBD]

`07 Oct 2025 - 15 Sep 2025`. Either bad stored data or a start/end transposition in rendering.
**Source unconfirmed** — those are different fixes with different owners. Needs a call.

---

## B28 — [P3] Log Water unit toggle converts the value but not the helper label · [FE]

Switching to fl oz converts the **value and the slider** but leaves **`1 glass = 250 ml`** metric.

**Now known to be a LOCAL defect, not a platform gap** — the Log Activity km/mi toggle was tested with the same
method and **passes** (5.0 km → 3.1 mi correctly, calories correctly unchanged). So the app *can* convert; one
modal does it right, one partially. Full comparison in `04-LOCALE-FORMATTING.md`.

---

## ✅ Functional checks that PASSED — recorded so they are not re-tested

| Check | Result |
|---|---|
| **Unit conversion — Log Activity km/mi** | **PASS.** 5.0 km → 3.1 mi correct; calories correctly unchanged |
| **Keyboard focus indicator** | **PASS.** A real Tab press shows `:focus-visible` at `2px solid rgb(101,74,183)` — see the correction note below |
| **Modal dismissal** | Escape closes the activity modals correctly |
| **Tab / sub-tab navigation** | Challenges `Ongoing / Upcoming / Past` switch correctly and update the view |
| **Card navigation** | Diary reachable via Summary's Snapshot card; Trends via Diary's Snapshot card |
| **Glyph rendering under interaction** | Arabic shaping stays correct through state changes |

### ⚠️ The focus-indicator "failure" was our own false negative

A programmatic `.focus()` suggested **4 of 10 controls had no focus indicator**. **That would have been a filed
bug.** Driving a **real Tab key** showed the indicator present on all of them.

**Cause:** `:focus-visible` deliberately does **not** activate for programmatic focus — that is correct browser
behaviour, not an app defect. **Any focus audit must drive real keyboard input.**

---

## Deliberate gaps in this file

| Gap | Why, and what would close it |
|---|---|
| ~~**Toast localization on a successful write**~~ | **CLOSED 2026-07-30 — and the answer is that there is no success toast to localize.** One Hiking activity was submitted with the user's authorisation; debt recorded in `../TEST_DATA_DEBT.md` beforehand. Observer installed **before** the modal opened, 2.5 s wait: `POST /activity/save` → **200**, record created, modal closed, **0 toasts**. **This gap cannot be a translation defect — the feature is absent, not untranslated.** Folded into the widened B31 |
| **Validation messages** | **Not reachable.** The Hiking form's submit is **not** gated (`disabled:false`, `aria-disabled:null`) but **every field ships a valid default**, so no invalid state can be reached without defeating the custom controls. **No validation message was reachable, so none could be checked for translation.** Distinct from the dashboard, where preventive gating was confirmed present |
| **Accented input in search** | **N/A — the premise was wrong.** `/ng/fit/programs` has **zero `<input>` elements**; there is no search box on Fit web. This gap was inherited from the dashboard, which *does* have search and *does* fail it. Still to confirm: no search exists on Community or Challenges either |
| **Create flows** | Challenges "+Add", Community create-event, Community add-post — **all unreached**, deferred on blast radius. This is where validation, toasts and dialogs all live, so it is the highest-value functional gap remaining |

---

# ═══ BACKEND ═══

| Item | Note |
|---|---|
| **B11** | The persistence half needs **backend** work — an account-level language field. Shared with the dashboard |
| **B14** | The empty paginated response is **backend** behaviour; the frontend renders what it receives |
| **B24** | 502 on `/marketplace/categories` — **environmental**, not a code defect |
| **B32** | Source unconfirmed — stored data vs render transposition. **Needs a call before assignment** |
| **B31** | **Frontend.** The dashboard's equivalent proved the *server* returns a correct, detailed error body and the *client* discards it. Worth checking whether the same is true here before routing anywhere near backend |
</content>
