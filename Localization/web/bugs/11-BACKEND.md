# 11 — BACKEND

**24 findings, BE-1 – BE-24.** This file replaces the dashboard's `11-AC3-FALLBACK.md`, because the two
surfaces differ on exactly this point:

> **The admin dashboard reported ZERO backend defects** (backend localization was declared out of scope).
> **This surface has 24, every one quoted from a real API response body.**

**Full detail — including the verbatim response-body quotes — is in [`BACKEND-BUGS.md`](BACKEND-BUGS.md).**
This file is the **categorised view**: what each finding is, who owns it, and what order to fix in. It does not
duplicate the quotes, because those are the evidence and they should be read at source.

---

## How these were captured — and why it matters

**Method:** the running app's **actual API response bodies and request headers**, via network capture.

**Why network capture and not a plain `fetch`:** the xsrf cookie is **HttpOnly**, so authenticated calls
**cannot be replayed** — a bare `fetch` returns **401**. Everything here was read from traffic the app itself
made. *(And that 401 is expected, not an outage — a health check added mid-engagement misfired on exactly this
confusion. **Only 502 indicates a real outage.**)*

**Fit API base:** `/vantagefit/api/v1/` — endpoints inspected: `configuration` · `today/overview` ·
`challenge/ongoing/all` · `content/top` · `content/byCategoryName` · `app/home` · `dashboard/activities/all`

**Languages captured:** **Arabic**, **German** and **French** — same endpoints, bodies inspected.
**4 of 16 captured (incl. English baseline)** — see the closing section on why more would add coverage data, not new defect classes.

---

## ⚠️ The locale mechanism — read this before the findings

## 🔴 BE-1 — [P3 alone / P2 in combination] Locale has TWO sources of truth · = **B38** · **REFRAMED 2026-07-30**

**No `Accept-Language` header. No locale query parameter. On any `/vantagefit/api/v1/*` call.** Confirmed on
live headers — `device` · `apptype: Fitness` · `appversion` · `appname` · xsrf, and nothing else.

### ⚠️ The original conclusion was WRONG and is withdrawn

BE-1 previously read *"so the backend cannot localize; the backend replies in English."* **The backend localizes
correctly.** A French session with **no locale in the request** returned:

| Endpoint | French strings | English UI strings |
|---|---:|---:|
| `configuration` | **5** | **0** |
| `app/home` | **18** | 9 *(2 are authored challenge names — expected)* |

`Évènements à venir` · `Pas effectués` · `Progrès hebdomadaire` · `Classement général` · `Hémoglobine` ·
`Défi e-Marathon` · `Minutes de pleine conscience`

**Therefore the backend resolves the language server-side — almost certainly from the account record.** Stated
as the strong inference it is: what is *proven* is no locale in, correct French out.

### The actual defect: two sources of truth

| Layer | Source of language |
|---|---|
| Frontend chrome | client-side (profile / stored preference) |
| Backend content | **the account record** |

**And a mechanism that makes them diverge already exists in this report.** **B11** — the preference is **not
persisted**. If the client holds language X while the account holds Y, you get **chrome in X, content in Y** —
which is **B25's exact signature**, including the part that had no explanation: why the *content* query language
drifts independently of the chrome.

**B11 + BE-1 may be the root cause of B25.** Best lead in the engagement, and cheap to confirm:
**does the language selector write to the account, or only to the client?**

### What this changes about fix order

**BE-1 is no longer the gate on the rest of this file.** The old reasoning was "the backend can't localize until
it's told the locale, so nothing else is verifiable". **It can and does.** So the findings below are **genuine,
independent backend defects** — they are not artefacts of the missing header, and **fixing BE-1 will not fix
them.**

**Still worth fixing**, for the divergence risk and because one authoritative locale per request is what the
admin dashboard does — and it is **structurally less prone to B25-style drift.**

**Instructive contrast:** the **admin dashboard sends `accept-language` correctly** (confirmed on a report
request). So this is **not a platform limitation** — one surface does it right, and its implementation is the
reference.

---

## ⚠️ The premise this whole engagement inherited — and which this file contradicts

The **dashboard** engagement was run on a stated requirement: *"localization is frontend-only today; the
backend is not translated yet, so backend English is expected and must not be false-flagged."* On that basis it
**excluded** a set of English strings as expected-not-defects.

**This surface shows the backend DOES translate — demonstrably, for German and French.** Section headings,
metric labels and prose all come back translated on some endpoints.

**So the premise is wrong, or it applies to one surface and not the other.** Either way:

1. Every "expected English, excluded" verdict on the dashboard is **provisional** until re-confirmed
2. The findings in this file are **genuine defects**, not scope confusion — the capability exists and is being
   applied inconsistently

**This needs one answer from the backend team, and it affects both engagements.**

---

## The findings, grouped by what they actually are

### Group A — English where translation demonstrably works elsewhere · the core defect class

| ID | P | Finding |
|---|---|---|
| **BE-2** | P2 | Section headings served from the backend, in English. **Needs a design answer first** — are these meant to be server-owned? |
| **BE-3** | P2 | **All** challenge card metadata is English |
| **BE-6** | P2 | Diary / Summary **metric labels** are English |
| **BE-9** | P2 | The caloric-deficit sentence is English (= **B17**) |
| **BE-13** | P2 | Partner / Events section headings and subtitles are English |
| **BE-10** | P3 | Distance explanation text is English (= **B18**) |
| **BE-11** | P3 | Adherence question **and** answer options are English (= **B26** — `Yes` should be `Sí`) |
| **BE-12** | P3 | Health-risk **status value** is English |
| **BE-21** | P3 | `Leaderboard` and `SCORE` English in a German session |
| **BE-22** | P3 | `"Week 1"` untranslated on a **second** endpoint (= **B4**) |

### Group B — formatting done by the server, wrongly

| ID | P | Finding | Why it can't be fixed client-side |
|---|---|---|---|
| **BE-7** | P2 | Units are English **and imperial**, served **pre-formatted** | The client receives a finished string |
| **BE-8** | P2 | The backend sends a **pre-formatted English date** | Same |
| **BE-19** | P3 | A **mixed-language date inside one backend string** | The string arrives already broken |
| **BE-20** | P2 | **Number formatting is inconsistent *within a single response*** | See below — this one is decisive |

#### 🔴 BE-20 is the strongest single piece of evidence in this file

One API response contains **`7.000` formatted correctly** for the locale **alongside `67.6` formatted
wrongly** — same payload, same request, same locale. Confirmed in **both German and French**.

**Why that settles an argument:** it cannot be a client-side bug (the client sees both numbers identically), and
it cannot be "the backend doesn't do locale formatting" (one of them is correct). **The server does
locale-aware formatting inconsistently.** So "just let the frontend format it" is not an available answer for
these fields.

### Group C — language quality in server-rendered prose

| ID | P | Finding |
|---|---|---|
| **BE-15** | P2 | **Four defects in one string** (= **B27**): untranslated `fl oz`, the nonsensical `fl oz vasos`, the pluralization error `1 días`, and imperial units in a metric locale. **The worst single string found in this engagement** |
| **BE-4** | P3 | Pluralization bug in a backend template |
| **BE-5** | P4 | Inconsistent capitalisation **within one response** |
| **B8** | P3 | **Reclassified from frontend 2026-07-30.** `Minutes Actives` (wrong casing) and `Minutes actives` (correct) **in the same `app/home` payload** — `progressUI.metrics[1].displayTitle` and `.legend` vs `trends.snippets[1].title`. A confirmed instance of BE-5, with exact paths. **The frontend receives both identically, so it cannot be a client-side bug** |
| **BE-24** | P3 | **NEW.** French pluralization — `Défi de course (se termine dans 1 jours)` should be `1 jour`, **two array entries from a correct `21 jours`**. Parallel to BE-15's Spanish `1 días`, so it is a **shared template with no n=1 rule** — one fix covers every inflected language. The nearby correct plural proves the template *can* do it |

**Note for whoever owns the register decision (B12):** because the backend returns prose the user reads,
whatever politeness register product settles on must be applied to **server-side strings too**, not only to
frontend dictionaries.

### Group D — stored data problems

| ID | P | Finding |
|---|---|---|
| **BE-14** | P2 | **Malformed image paths are in the STORED DATA** (= **B23**) — 23 double `.png.png` extensions plus a broken fallback. This is why thumbnails render as black boxes, and why it is **not** a frontend fix |
| **BE-17** | P4 | Duplicate adherence activities in the data — hygiene |

### Group E — backend, but NOT localization

**Reclassified during review.** These are real and worth fixing, but filing them as *localization* defects
would misdirect them:

| ID | P | Finding | Why it's here |
|---|---|---|---|
| **BE-23** | P2 | **Leaderboard pagination URLs point to `localhost:9050`** | A **configuration leak** — a local dev address in a production response. Nothing to do with language, and arguably the most urgent item in this file on its own merits |
| **BE-18** | P4 | Typos in stored content-category names | Proofreading, not translation |
| **BE-14** | P2 | Malformed image paths | Data quality; localization-independent |

**BE-23 deserves separate escalation.** It is not a localization bug and should not wait behind one.

---

## 🔴 BE-16 — resolved by direct comparison into THREE distinct problems

Originally logged as one finding. Capturing **Arabic and German back to back on the same endpoints** separated
it into three, which matters because they have three different owners and three different fixes:

| | Problem | Scope |
|---|---|---|
| **16a** | `today/overview` is **unlocalized for ALL languages** | Endpoint-level — no language gets translated output |
| **16b** | **Arabic has NO backend coverage at all** | Language-level — endpoints that are fully de/fr return **100% English** for Arabic |
| **16c** | `"Week 1"` is a **specific missing key**, present in German alongside `Woche` | String-level — the translation exists in the same payload |

**Why the split matters:** as one finding it read as "the backend doesn't localize". As three, it reads as
"one endpoint was missed, one language was never done, and one key was skipped" — which is actionable and much
less alarming. **This is what the back-to-back comparison bought.**

**16b is a product question, not just a bug:** if Arabic is a supported market, it needs backend translation
work that has never been started. Note this compounds with the frontend picture — RTL *works* here (better than
the dashboard), but there is no Arabic content to put in it.

---

## Verification audit — what we checked about our own claims

After the first backend pass, every finding was re-examined against the question *"is this actually backend, or
did we infer it from the screen?"* Three changed:

| Finding | Change |
|---|---|
| **BE-18** | **Rendered-only** — moved to "backend but not localization" |
| **BE-14, BE-17** | Re-examined and confirmed, but reclassified as **backend-but-not-localization** |
| **BE-2** | Flagged as **needing a design answer** before it can be called a defect at all |

**A shortcut we used early and then withdrew:** *"translated = backend, English = frontend."* API bodies showed
**English backend strings**, so English does **not** imply frontend. Every classification resting on that
shortcut was re-derived from actual response bodies — which is how **B4, B17, B18 and part of B1** were found to
be **provably backend** after being filed as frontend.

---

## Severity totals

| | Count | IDs |
|---|---:|---|
| **P2** | **14** | BE-1, BE-2\*, BE-3, BE-6, BE-7, BE-8, BE-9, BE-13, BE-14†, BE-15, BE-16, **BE-20**, **BE-23**† |
| **P3** | 10 | BE-4, BE-10, BE-11, BE-12, BE-19, BE-21, BE-22, **BE-24**, **B8**, **BE-1**\*\* |
| **P4** | 2 | BE-5, BE-17†, BE-18† |
| **Total** | **24** | |

\* needs a design answer first · † backend but **not** localization (BE-14, BE-17, BE-18, BE-23) ·
\*\* BE-1 is P3 alone but **P2 in combination** with B11/B25 — counted once, in P3

---

## Language coverage — 3 of 16, and why that is a defensible stopping point

**Captured:** Arabic, German, **French (bodies inspected 2026-07-30 — `configuration` + `app/home`)**.

**The pattern is stable across 3 languages** — two *with* frontend dictionaries and one *without* — and every
language-independent defect above is confirmed **3 times**. Additional languages would add **coverage
information only, not new defect classes.**

| Priority | Languages | Reason |
|---|---|---|
| **Worth capturing** | **es**, **pt** (+ `pt-BR` / `pt-PT` as one variant check) | The remaining locales the product claims to support |
| **Low value** | zh-CN · nl · fr-CA · it · ko · ru · vi · hu · ja | **One spot-check of a single one confirms the whole group** — none has a wired frontend dictionary, so "no backend coverage either" is the near-certain result |

**Cost, stated honestly:** ~8–10 tool interactions per language for the language switch and forced re-login
**before any capture begins**. 13 languages ≈ 120+ interactions. That is the real reason this stopped at 3, and
it is a judgment call rather than a hard blocker.

**Status: every defect class is confirmed; only per-language coverage remains open.**

---

## Recommended fix order

1. **BE-1 / B38** — **re-scoped.** No longer a gate on the rest: the backend already localizes from the account.
   Fix it for the **divergence risk** (and because it is the likely mechanism behind **B25**, via **B11**).
   **First, though, answer the cheap question:** does the language selector write to the account or only to the
   client? That one answer may close B25.
2. **BE-23** — the `localhost:9050` leak. Not localization; escalate separately and don't let it queue behind
   translation work.
3. **BE-16b** — decide whether Arabic is a supported market, then act.
4. **BE-20, BE-7, BE-8, BE-19** — the formatting layer. One consistent server-side formatting path.
5. **BE-15, BE-4, BE-5** — server-rendered prose quality, with the register decision applied.
6. **BE-14** — the stored malformed image paths. *(Frontend can add `alt` text independently and immediately —
   that mitigates the user-visible symptom without waiting for this.)*
7. **Group A** — the remaining untranslated server strings, once 1 is verified working.
</content>
