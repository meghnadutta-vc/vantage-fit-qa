# B33 + B38 — Fit is not translating. Two separate causes, both proven.

**For:** developer / tech lead confirmation
**Tested:** 2026-07-30 · `app.vantagecircle.co.in/ng/fit/*` · UAT · account language = **Arabic**
(also reproduced in German and French)

---

## In one paragraph

The Fit web app shows almost everything in English even when the user's language is set to something else.
There are **two independent causes**, and both need fixing:

1. **The frontend translation file is not being served** — the server returns the website's homepage HTML
   instead of the translation JSON, for every language including English.
2. **The frontend never tells the backend which language the user chose** — no `Accept-Language` header, no
   locale parameter. So the backend replies in English too.

Neither is a translation-content problem. The translations are not the issue; the plumbing is.

---

# CAUSE 1 — B33: the frontend translation file is not served

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
