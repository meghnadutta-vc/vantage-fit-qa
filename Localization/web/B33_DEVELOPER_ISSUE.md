# B39 + B38 — Fit is not translating. Root cause found; this supersedes the earlier B33 framing.

**For:** developer / tech lead confirmation
**Tested:** 2026-07-30 · `app.vantagecircle.co.in/ng/fit/*` · UAT · account language = **Arabic**
(also reproduced in German and French)

> ## ⚠️ CORRECTED 2026-07-30 — read this before the rest of the document
>
> An earlier version of this write-up described **cause 1** as *"the frontend translation file is not being
> served"*, which reads as a **deployment or server-route bug with a cheap fix.** Further investigation
> (bundle inspection — see **B39** in `bugs/bug-log.md`) shows that is **not the situation**, and acting on
> the earlier framing would waste the team's time.
>
> **What is actually true:** the Fit web module **has no internationalization mechanism at all.** Its
> interface strings are compiled into the JavaScript bundle as **static template literals**. Measured across
> 101 loaded scripts (5.1 MB): the Fit chunk is the **largest bundle in the app** and contains **0** of the
> app's **79** translation calls — no `translate` pipe, no `TranslateService`, no `.instant()`, no
> `$localize`. Meanwhile one sibling chunk alone has **71**.
>
> **So serving a translation file would change nothing** — there is no code path that would read it. The
> `/ng/assets/i18n/fit/` path returns the SPA shell for **any** filename, including `zzz.json` and
> `NOT-A-LOCALE.json`, at an identical 115,655 bytes. That directory was never real; it is the SPA catch-all.
>
> | | Earlier framing (B33) | **Corrected (B39)** |
> |---|---|---|
> | Problem | The Fit translation file is not served | **The Fit module cannot consume a translation file** |
> | Fix | Fix asset copy / server route | **Internationalize the Fit module** — add the translation service, externalise every string, author dictionaries |
> | Effort | Hours | **Substantial project** |
>
> The evidence table below is still valid and is retained — it is now **a symptom** of B39 rather than the
> cause. **B38 (cause 2) is unaffected and stands exactly as written.**

---

## In one paragraph

The Fit web app shows almost everything in English even when the user's language is set to something else.
There are **two independent causes**, and both need fixing:

1. **The Fit frontend was built without any translation support** — its text is hardcoded into the compiled
   bundle, so no language selection can change it. The rest of the product (Rewards, My Account, Perks) does
   use a translation service; Fit does not.
2. **The frontend never tells the backend which language the user chose** — no `Accept-Language` header, no
   locale parameter. So the backend replies in English too.

Neither is a translation-content problem — nobody mistranslated anything.

**Why the product looks *partly* translated, which is the confusing part:** the text that **does** appear in
the user's language comes from the **backend**, and the text that stays English is the **frontend's own**.
Live proof from a single French session on `/ng/fit/summary` — French where the API supplies the label,
English where the bundle does:

| From the API → **translated** | From the bundle → **always English** |
|---|---|
| `Pas` · `Progrès hebdomadaire` · `Hémoglobine` · `Mis à jour le 17 Jan 2024` | `Challenges` · `Wellness Score` |

`Challenges` appears in the bundle as the literal `d(3,"Challenges")`. Evidence:
`evidence/fr_summary_hardcoded_chrome_vs_api_labels.png`

---

# CAUSE 1 — B39: the Fit module has no translation mechanism

*(The section below was written as B33 — "the file is not served". It is retained because the measurements
are correct and reproducible, but read it as **a symptom of B39**, not as the cause. See the corrected
summary at the top.)*

## Proof A — the Fit bundle contains none of the app's translation calls

Same application, same build. Scanned all 101 loaded scripts (5.1 MB):

| Bundle | Size | Fit markers | translation calls |
|---|---:|---:|---:|
| **the Fit module chunk** | **1,072 KB** | **282** | **0** |
| one sibling chunk | 284 KB | 0 | **71** |
| main | 393 KB | 0 | 4 |
| two others | 364 KB | 0 | 4 |
| | | | **non-Fit total: 79** |

## Proof B — the strings are literals in the compiled template

`d(...)` is Angular's static-text instruction, not a key lookup:

```js
r(6,"h2"), d(7,"Calorie Ledger"), l()          // …and d(14,"Food Log")
r(8,"h3",26), d(9,"Health bites")              // …and d(12,"15-30 sec tips")
r(2,"h1"), d(3,"Challenges"), l(), r(4,"p"), d(5,"Compete with peers & colleagues, track your tasks.")
```

## Proof C — the i18n path is the SPA catch-all, not a missing file

A filename that **cannot** be a locale returns the same bytes as a real one:

| Requested | Status | `content-type` | Bytes |
|---|---|---|---|
| `/ng/assets/i18n/fit/de.json` | 200 | `text/html` | 115,655 |
| `/ng/assets/i18n/fit/zzz.json` | 200 | `text/html` | 115,655 |
| `/ng/assets/i18n/fit/NOT-A-LOCALE.json` | 200 | `text/html` | 115,655 |

## Proof D — the app never asks for a Fit dictionary

Resource timing shows exactly **one** app-initiated i18n request in the session:
`/ng/assets/i18n/fr.json`. There is **no** app-initiated request to any `fit/` path.

## Proof E — the working dictionary has no entry for today's Fit interface

`/ng/assets/i18n/fr.json` (1,460 keys) has a `fit.*` namespace of only **48 legacy keys**
(`fit.theme_of_the_week`, `fit.leaderboard`, `fit.fit_point_earned`). Of **12** live Fit UI strings searched
by value in `en.json` — `Steps`, `Active Minutes`, `Wellness Score`, `Challenges`, `Ongoing`, `Upcoming`,
`Water Intake`, `Avg Sleep`, `Weekly Rank`, `Highlights`, `Featured Content`, `Calorie Ledger` —
**0 of 12 are present.**

## What we have NOT proven — please correct us if you can

- Only chunks **loaded in this session** were scanned. A Fit chunk for an unvisited route could exist, so
  "0 translation calls in Fit" is proven for the principal Fit bundle, not for every byte of Fit code.
- 5 of the 12 probe strings were not found in any loaded chunk either — they may be in an unloaded chunk or
  may come from the API. **Recorded as inconclusive.**
- This is static analysis plus rendered-output correlation, **not a source review.** You can confirm or
  refute all of it in minutes with repo access, and we would rather be corrected than believed.

---

## Original B33 evidence — retained

## Proof — three requests, made side by side in the same session

| Request | Status | `content-type` | Valid JSON? | Contents |
|---|---|---|---|---|
| `/ng/assets/i18n/de.json` *(rewards/perks file)* | 200 | **application/json** | ✅ yes | **1,472 German phrases** |
| `/ng/assets/i18n/fit/de.json` *(Fit file)* | 200 | **text/html** | ❌ no | `<!DOCTYPE html> <html lang="en"…` |
| `/ng/assets/i18n/fit/en.json` *(Fit file)* | 200 | **text/html** | ❌ no | the same document, **byte-for-byte** |

**The clincher:** the German and English Fit files are **identical in size — 115,655 bytes each**. If two real
translation files were being returned they would differ. They are the same wrong file — the SPA `index.html`,
served because requests to `/ng/assets/i18n/fit/` fall through to the catch-all route.

Reproduced for `de`, `en` and `fr`. All three: 200, `text/html`, 115,655 bytes.

## What it looks like to a user — % of on-screen words actually translated (German)

| Programs | Diary | Trends | Community | Summary | Challenges |
|---:|---:|---:|---:|---:|---:|
| **0 %** | 3 % | 5 % | 9 % | 16 % | 20 % |

French and Arabic show the same pattern.

## It used to work — this is a regression

Words that rendered correctly in German on **24–28 July** — `Tagebuch`, `Kalorienbilanz`, `Vitalwerte`,
`Momentaufnahme`, `Herausforderungen`, `Bibliothek`, `Schlaf` — exist in **neither** loadable file today.
Something changed between then and now.

## Proof that nothing else is broken

In the **same Arabic/German session**, the rewards side of the site translates perfectly:
`Mein Profil` · `Meine Informationen` · `Meine Kontoeinstellungen` · `Arbeitsinformationen` ·
`Bevorzugte Sprache:` · `Änderungen speichern`.

Same browser, same user, same language setting, same translation mechanism. **The only difference is that the
rewards file is served correctly and the Fit file is not.** This rules out the language setting, the app
logic, and the translation system.

## Likely fix
A server route / rewrite rule so `/ng/assets/i18n/fit/*.json` serves the real files instead of falling
through to `index.html`. **Not application code.**

## Confirming the fix
Re-measure the six percentages above. If they do not rise sharply, the fix has not landed.

---

# CAUSE 2 — B38 (NEW): the frontend never sends the user's language to the API

## Proof — the actual request headers of a live Fit API call

`GET /vantagefit/api/v1/configuration` — captured from the running app, Arabic session:

```
device: web
apptype: Fitness
appversion: 3.2.0
appname: VantageFit
accept: application/json, text/plain, */*
x-xsrf-token: …
referer: https://app.vantagecircle.co.in/ng/fit/summary/diary
```

**There is no `Accept-Language` header, and no `lang`/`locale` parameter anywhere in the request.**
The backend is never told the user picked Arabic.

## The consequence, in the same call's response

`/vantagefit/api/v1/configuration` — Arabic session, **zero Arabic characters in the whole response**:

```json
"sponsoredLinks": { "heading": "Partner Offerings",
                    "subtitle": "To take care of your comprehensive wellness needs" }
"otherLinks":     [{ "heading": "Upcoming Events",
                     "subtitle": "See what wellness activities are happening in your company" },
                   { "heading": "Past Events" }]
"hra":            { "status": "Below Average" }
"adherenceActivities": [{ "title": "Did you go for a morning walk today?",
                          "subtitle": "A simple walk can energize your body and clear your mind.",
                          "options": [ { "displayText": "No" }, { "displayText": "Yes" } ] }]
```

`/vantagefit/api/v1/today/overview` — Arabic session, also **zero Arabic**:

```json
"date": "Today, 30 Jul 2026"
"distanceData": { "subtext": "The Distance moved is an estimate of the distance you have covered…",
                  "data": [ { "dataType": "Moved",   "value": "0.0 mile" },
                            { "dataType": "Running", "value": "0.0 mile" },
                            { "dataType": "Cycling", "value": "0.0 mile" } ] }
"nutritionData": { "data": [ { "dataType": "Meals", "value": "0.0 cal" },
                             { "dataType": "Water", "value": "0.0 fl oz" } ] }
"vitalsData":    { "data": [ { "dataType": "Mood" }, { "dataType": "Weight" },
                             { "dataType": "Heart Rate" } ] }
"intakeCaloriesData": { "subText": "You are currently in a caloric deficit" }
```

**The backend is sending display-ready English text, including units (`mile`, `fl oz`, `cal`) and a formatted
English date (`Today, 30 Jul 2026`).** The frontend prints these as-is.

## Likely fix
Send the user's locale on every Fit API call (`Accept-Language`, or an explicit parameter), **and** have the
backend honour it. Note the backend may also be able to read the language from the user's account record —
worth confirming which approach is intended.

---

# ⚠️ This corrects our own bug classifications

Several bugs we had filed as **frontend** are provably **backend** — the strings are in the API body:

| Bug | Was | **Actually** | Proof |
|---|---|---|---|
| **B17** "You are currently in a caloric deficit" untranslated | FE | **BACKEND** | `intakeCaloriesData.subText` in `today/overview` |
| **B18** "mile" unit not translated | FE | **BACKEND** | `"value": "0.0 mile"` in `today/overview` |
| **B1** English dates (part of it) | FE | **partly BACKEND** | `"date": "Today, 30 Jul 2026"` |
| Distance/Vitals/Nutrition labels (`Moved`, `Water`, `Mood`, `Weight`, `Heart Rate`) | — | **BACKEND** | `dataType` fields |
| **B26** adherence answers "Yes"/"No" | BE (correct) | **BACKEND — confirmed in the body** | `options[].displayText` |

And these **are** frontend (they appear in **no** API response, so they must come from the missing
dictionary): `Diary` · `Snapshot` · `Calorie Ledger` · `Food Log` · `Intake` · `Bedtime` · `Wake up` ·
`Log Water` · `Log Sleep` · `Log Activity` · all nav tabs.

**Practical consequence: fixing B33 alone will NOT make the app fully translated.** It fixes the frontend
half. The backend half needs B38.

---

# What we need confirmed

1. **B33** — is `/ng/assets/i18n/fit/` misrouted? Was it changed recently (it worked on 24–28 July)?
2. **B38** — is the locale *meant* to be sent to the Fit API? If the backend reads it from the account
   instead, why do these two endpoints return English?
3. Some backend text **does** come back translated — e.g. `E-Marathon-Herausforderung (endet in 22 Tagen)`,
   `Gagnez 10 000 Fit Points`, `Terminé`. So **some** endpoints localize and these two do not.
   **Which is the intended behaviour?**

## How to reproduce everything above
1. Profile → Edit Profile → Language → **Arabic** (or German) → Save → re-login.
2. Open `/ng/fit/summary/diary`.
3. DevTools → Network. Look at `/ng/assets/i18n/fit/<lang>.json` → **response is HTML, not JSON**.
4. Look at `/vantagefit/api/v1/configuration` → **request has no `Accept-Language`; response is all English**.
