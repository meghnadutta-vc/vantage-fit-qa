# 01 — P1 & P2: FIX FIRST

**1 P1 · 16 P2.** Ordered by **fix leverage**, not by bug number. This is the only file that **repeats**
bugs — each also appears in its type-specific file, tagged as a repeat. **Fix from here.**

**"One P1" is a tested result.** Three data-integrity candidates were hunted specifically; the unit-toggle
conversion passes and nothing was found that silently corrupts stored data. See `09-NOT-A-BUG.md`.

**This order is also the recommended Jira ticket order** — grouped by fix unit, so one ticket closes one
change.

---

# ═══ TIER 1 — the architectural root cause ═══

## 🔴 B39 — [P1] The Fit web module has NO internationalization mechanism · [FE]

*(Whole module · all 5 modules · all 16 profile languages)*

Fit's interface strings are compiled into the JavaScript bundle as **static template literals**. There is no
translation layer, so **no language selection can ever change them.**

### Proof — six independent methods agree

**1. The Fit bundle contains none of the app's translation calls.** Same app, same build; 101 loaded scripts,
5.1 MB scanned:

| Bundle | Size | Fit markers | `\|translate` | `TranslateService` | `.instant(` | `$localize` | **total** |
|---|---:|---:|---:|---:|---:|---:|---:|
| **the Fit chunk** | **1,072 KB** | **282** | 0 | 0 | 0 | 0 | **0** |
| one sibling chunk | 284 KB | 0 | 0 | 0 | **71** | 0 | **71** |
| main | 393 KB | 0 | 0 | 0 | 4 | 0 | 4 |
| two others | 364 KB | 0 | 0 | 0 | 0 | 4 | 4 |
| | | | | | | **non-Fit** | **79** |

**The largest chunk in the app is the only one with 0 of 79.**

**2. The strings are literals in the compiled template** (`d(...)` is Angular's static-text instruction, not a
key lookup):

```js
r(6,"h2"), d(7,"Calorie Ledger"), l()          // also d(14,"Food Log")
r(8,"h3",26), d(9,"Health bites")              // also d(12,"15-30 sec tips")
r(2,"h1"), d(3,"Challenges"), l(), r(4,"p"), d(5,"Compete with peers & colleagues, track your tasks.")
```

**3. The on-screen split matches the code split exactly.** Live French session, `/ng/fit/summary`:

| Translated (from the API) | English (from the bundle) |
|---|---|
| `Pas` · `Progrès hebdomadaire` · `Hémoglobine` · `Classement hebdomadaire` · `Votre dernier badge` | `Summary` · `Challenges` · `Programs` · `Community` · `Snapshot` · `Open Diary` · `View Trends` · `Wellness Score` · `Add` |

**4. The i18n path is the SPA catch-all, not a missing file.** A filename that cannot be a locale returns the
same bytes as a real one:

| Requested | Status | Content-Type | Bytes |
|---|---|---|---|
| `/ng/assets/i18n/fit/de.json` | 200 | `text/html` | 115,655 |
| `/ng/assets/i18n/fit/zzz.json` | 200 | `text/html` | 115,655 |
| `/ng/assets/i18n/fit/NOT-A-LOCALE.json` | 200 | `text/html` | 115,655 |

**5. The app never requests a Fit dictionary.** Resource timing shows exactly one app-initiated i18n request
(`initiatorType: xmlhttprequest`): `/ng/assets/i18n/fr.json`. No app-initiated `fit/` request exists.

**6. The working dictionary has no entry for today's Fit interface.** `fr.json` (1,460 keys) has a `fit.*`
namespace of only **48 legacy keys** (`fit.theme_of_the_week`, `fit.leaderboard`, `fit.fit_point_earned`). Of
12 live Fit UI strings searched by value in `en.json` — `Steps`, `Active Minutes`, `Wellness Score`,
`Challenges`, `Ongoing`, `Upcoming`, `Water Intake`, `Avg Sleep`, `Weekly Rank`, `Highlights`,
`Featured Content`, `Calorie Ledger` — **0 of 12 are present.**

### What this costs to fix

**This is a project, not a ticket.** Add the translation service to the Fit module, externalise every string,
author 16 dictionaries. Sizing that is engineering's call, but it should not be scoped from B33's framing.

### Honest limits

- Only chunks **loaded in this session** were scanned. A Fit chunk for an unvisited route could exist, so
  "0 translation calls" is proven for the **principal** Fit bundle, not every byte of Fit code.
- 5 of 12 probe strings weren't found in any loaded chunk either — they may be in an unloaded chunk or come
  from the API. **Inconclusive**; a bundle miss is not proof of absence.
- Static analysis + rendered-output correlation, **not a source review.** A developer can confirm or refute in
  minutes and should be asked to.

**Evidence:** `../evidence/fr_summary_hardcoded_chrome_vs_api_labels.png`

---

## 🔴 B33 — [P1] The Fit i18n endpoint serves the SPA HTML shell · [FE] · **CLOSE INTO B39**

**Retained as the observable symptom** (it is proof 4 above) and because a developer write-up already went out
under this ID. **Do not file it separately.**

**Why the reframe matters:** B33 as originally written implies *"the file exists but isn't served"* → a cheap
deployment fix. **Serving a dictionary to a module with zero translation calls changes nothing.**
`B33_DEVELOPER_ISSUE.md` has been corrected with a banner at the top; if anyone received the earlier version,
they need the correction.

---

# ═══ TIER 2 — the backend half of the same user-visible problem ═══

## B38 — [P2] The frontend never sends the user's locale to the Fit API · [FE] · = **BE-1**

No `Accept-Language` header, no locale query parameter, on **any** `/vantagefit/api/v1/*` call. Confirmed on
live headers: `device` · `apptype: Fitness` · `appversion` · `appname` · xsrf — and nothing else.

### ⚠️ CORRECTED 2026-07-30 — the observation stands, the stated impact was wrong

B38 originally concluded *"so the backend cannot localize."* **It can, and it does.** A French session with
**no locale in the request** returns correctly French bodies — `Évènements à venir`, `Pas effectués`,
`Progrès hebdomadaire`, `Hémoglobine`. The backend resolves the language **server-side, from the account**.

**So the real defect is not a missing header — it is two sources of truth for locale:**

| Layer | Source of language |
|---|---|
| Frontend chrome | client-side (profile / stored preference) |
| Backend content | **the account record** |

**This may be the mechanism behind B25.** **B11** says the preference **is not persisted** — so the client can
hold language X while the account holds Y, producing **chrome in X and content in Y**. That is precisely B25's
signature, *including the part that had no explanation*: why the content query language drifts independently.

**Cheap for a developer to confirm or kill:** does the language selector write to the **account**, or only to
the client?

**Severity:** **P3** as an isolated architectural smell; **stays P2 in combination**, because it is a candidate
mechanism for B25. **Do not close it — re-scope it.**

**B39 and B38 are both required.** Fixing B39 alone leaves all API-supplied text English; fixing B38 alone
leaves all chrome English. Neither is sufficient. **This is the single most important sequencing fact in the
report.**

**Instructive contrast:** the admin dashboard **does** send `accept-language` correctly. So this is not a
platform limitation — one surface does it right.

---

# ═══ TIER 3 — language state is unreliable ═══

## B25 — [P2] Runtime language desyncs from `<html lang>` and the saved preference mid-session · [FE]

No re-login, no language change. Reproduced on **4 consecutive fresh loads**, confirmed on Summary, Programs
**and** Challenges. **It corrupts backend content queries too**, not just chrome — Programs served the full
English content set instead of the Spanish-scoped set seen earlier the same day.

**Consequence for this whole report: every pass result is point-in-time only.** Re-check a sample late in any
long session.

**Partly explained by B39.** The asymmetry — content changes with language, chrome never does — *is* the
signature of hardcoded chrome. What remains unexplained is why the **content** query language drifts. Needs
dev access to the language-state code.

## B11 — [P2] Language preference not persisted — reverts to English after re-login · [FE/BE]

The admin's choice does not survive session expiry. Same defect exists on the admin dashboard (logged there as
F8#1), so **one account-level fix should serve both surfaces** rather than being done twice.

---

# ═══ TIER 4 — Arabic / RTL ═══

## B35 — [P2] Numeric, unit and date runs render in REVERSED visual order · [FE]

Arabic session. **The DOM order is correct; the painted order is wrong** — no bidi isolation around
neutral-directionality runs. **Invisible to text extraction**, which is why it went unfound for the whole
engagement until a dedicated painted-position detector was built.

**Note the opposite result from the dashboard:** RTL **is implemented** on this surface (`dir` is set, layout
mirrors). The dashboard has no RTL at all. **The dashboard's AR#1 must not be copied here.**

**Detector caveat, stated because it matters for anyone re-running it:** the first two versions of this
detector were **wrong** — it flagged correctly-rendered Arabic as reversed and would have produced 7 false
bugs on Challenges Ongoing where the true count is 0. Two guards are mandatory: a **y-band guard** (RTL lines
restart from the right, so cross-line comparison is meaningless) and a **script guard** (only flag runs
containing no Arabic characters). See `03-UI-LAYOUT.md`.

---

# ═══ TIER 5 — data and content integrity ═══

## B23 — [P2] Programs thumbnails render as solid black boxes · [FE] · = **BE-14**
Malformed CDN URLs — 23 with a double `.png.png` extension, plus a broken fallback. **The malformed paths are
in the stored data**, so the fix is backend/content, not frontend. Confirmed again in Phase 2: **20 console
errors on a single Programs navigation.**

**Compounds the accessibility findings:** these images also have no `alt`, so the user gets **neither the
image nor a text fallback**.

## B27 — [P2] Water weekly-task sentence garbled — four defects in one string · [FE] · = **BE-15**
Untranslated `fl oz`, the nonsensical `fl oz vasos`, and the pluralization error `1 días`. **Server-rendered**,
so it is a backend string fix.

## B31 — [P2] Log Water submit with no amount closes the dialog with zero feedback · [FE]
No toast, no inline error, no validation block — the dialog simply closes. **Toast absence was confirmed with
the observer installed before the action and a wait**, so this is not a timing artefact.

Same class as the dashboard's silent-failure family (UP#4 / SET#4). **Worth checking whether other Fit write
flows discard failures the same way** — that would multiply one P2.

---

# ═══ TIER 6 — untranslated strings and formatting (all downstream of B39) ═══

**Read these as *instances* of B39, not as independent wire-up gaps.** They are listed separately because they
were found and logged separately, and because they tell you *which surfaces* a user notices first.

| Bug | P | What |
|---|---|---|
| **B1** | P2 | Dates render in English format and English words in **every** language. French session shows `Thursday, 30 July 2026` and `20th Feb 2026`; also `2:33 PM` 12-hour time and `5.0` decimal point where French needs `14:33` and `5,0` |
| **B2** | P2 | The language-change confirmation alert prints a **literal `{language}` placeholder** in de/fr/es — only English interpolates it |
| **B3** | P2 | `Challenges` nav tab untranslated in German **while German body text says `Herausforderung`** — the same concept in two languages on one screen |
| **B4** | P2 | `Week 1` challenge week label untranslated in **any** language |
| **B5** | P2 | Highlights social strings — `Posted by` / `Likes` / `Comments` / `2 days ago` |
| **B12** | P2 | Formal/informal register mixed, **3 languages confirmed** on the **same 3 structural positions** (de `Ihr`, es `Su/sus/Camine`, fr `Votre/vos/Faites`) — the shared positions suggest one shared source string, not three independent slips |
| **B14** | P2 | Health-bites "view all" opens an **empty content grid** — a locale-handling gap on a paginated endpoint |
| **B16** | P2 | Community module chrome **0% localized**, and the nav/footer regress to English while on that route |
| **B17** | P2 | `You are currently in a caloric deficit` untranslated (= **BE-9**) |
| **B19** | P2 | Trends page mostly unlocalized, and **Spanish also drags the nav down while German doesn't** — inconsistent even within itself |
| **B20** | P2 | Diary chrome + nav regress to English **in Spanish** — Diary was the *best*-localized screen in German. **This is the clearest proof that module quality does not transfer between languages** |

---

## The one number that frames all of Tier 6

**Module quality does not transfer between languages.** B14 is German-only (Spanish is fine); B20 is
Spanish-only; Diary is the best screen in German and ~90% English in Spanish. **A German-only pass
systematically misreports this surface** — every (module × language) pair needs independent verification.
</content>
