# Vantage Fit Web — BACKEND localization bugs

> **📁 This file is now the DETAIL source for [`11-BACKEND.md`](11-BACKEND.md).**
> `11-BACKEND.md` is the categorised view — groups, owners, severity totals and fix order.
> **This file holds the verbatim API response-body quotes**, which are the actual evidence. Both are current;
> neither supersedes the other. Start at `11-BACKEND.md`, come here for proof.


**Surface:** employee Fit web · `app.vantagecircle.co.in/ng/fit/*` · UAT
**Method:** captured the running app's **actual API response bodies and request headers** (network capture,
which carries the app's auth — a bare `fetch` returns 401). Session language = **Arabic**; cross-checked
against German and French.
**Compiled:** 2026-07-30 · Source of record: `bug-log.md`

> **Every finding below is quoted from a real API response body.** These are not inferences from the screen.
> **All of these need BACKEND work.** Fixing the frontend dictionary (B33) will not resolve any of them.

**Fit API base:** `/vantagefit/api/v1/` — endpoints inspected: `configuration` · `today/overview` ·
`challenge/ongoing/all` · `content/top` · `content/byCategoryName` · `app/home` · `dashboard/activities/all`

**Languages captured:** **Arabic** and **German** (same endpoints, same data, back to back) — see **BE-16**
for the three-way comparison that scopes each problem. French still to capture.

---

## BE-1 — [P2] **ROOT CAUSE** · The frontend sends no locale, so the backend cannot localize (= **B38**)

**Request headers captured from a live `GET /vantagefit/api/v1/configuration`:**
```
device: web · apptype: Fitness · appversion: 3.2.0 · appname: VantageFit
accept: application/json, text/plain, */* · x-xsrf-token: … · referer: …
```
**No `Accept-Language`. No `lang` / `locale` parameter.**

**Open question for the team:** if the backend is meant to read the language from the user's account record
instead of a header, why do `configuration` and `today/overview` return English while challenge content
returns German/French? See **BE-16**.

---

## BE-2 — [P2] Section headings are served from the backend, in English
**Endpoint:** `GET /vantagefit/api/v1/content/top` · field `textConfig`
```json
"textConfig": {
  "general":  { "heading": "Health Content",   "subtitle": "To improve your Lifestyle" },
  "featured": { "heading": "Featured Content", "subtitle": "Curated Health Content for you" } }
```
These are **UI headings, not content data**, and they arrive in English. **This is why the Programs module
measures 0 % translated** — its headings are backend-served.
*Also a copy issue: "Lifestyle" is capitalised mid-sentence.*

## BE-3 — [P2] All challenge card metadata is English
**Endpoint:** `GET /vantagefit/api/v1/challenge/ongoing/all`
```json
"subtitle": "Week 1"
"subtitle": "Next Milestone: Machu Pichu"
"subtitle": "e-Marathon Challenge (ends in 21 days)"
"subtitle": "Race challenge (ends in 1 days)"
"progressTitle": "Weekly progress: 0%" / "Milestone progress: 0%" / "Overall progress: 0%" / "0 steps completed"
"rankTitle": "Weekly Rank" / "Overall Rank"
```
**This is the source of B4 ("Week 1" untranslated), which we had filed as a frontend bug. It is backend.**

## BE-4 — [P3] Pluralization bug in a backend template
`"Race challenge (ends in 1 days)"` — should be **"1 day"**.
Same defect in French: `pendant 1 jours` (should be `1 jour`). **A backend string template with no plural rule.**

## BE-5 — [P4] Inconsistent capitalisation within one response
`"e-Marathon Challenge"` (capital C) vs `"Race challenge"` (lowercase c) — same field, same payload.

## BE-6 — [P2] Diary / Summary metric labels are English
**Endpoint:** `POST /vantagefit/api/v1/today/overview`
```json
"dataType": "Moved" / "Running" / "Cycling"          (distanceData)
"dataType": "Meals" / "Water"                        (nutritionData)
"dataType": "Mood" / "Weight" / "Heart Rate"         (vitalsData)
```

## BE-7 — [P2] Units are English **and imperial**, served pre-formatted
```json
"value": "0.0 mile"    "value": "0.0 fl oz"    "value": "0.0 cal"
```
**Source of B18 ("mile" untranslated), which we had filed as frontend. It is backend.**
Two defects in one: the unit **word** is untranslated, and **imperial units are served to a metric locale**.
The frontend cannot fix either — the value arrives as a formatted string.

## BE-8 — [P2] The backend sends a pre-formatted **English** date
```json
"date": "Today, 30 Jul 2026"
```
**Part of B1's cause.** Because the date is pre-formatted server-side, no client-side locale formatter can
correct it. **In Arabic this is worse than cosmetic** — it feeds the RTL reversal bug (B35), which renders
date ranges backwards, e.g. `24 - 30 Jul` displaying as `Jul 30 - 24`.

## BE-9 — [P2] Caloric-deficit sentence is English
```json
"intakeCaloriesData": { "subText": "You are currently in a caloric deficit" }
```
**Source of B17, which we had filed as frontend. It is backend.**

## BE-10 — [P3] Distance explanation text is English
```json
"distanceData": { "subtext": "The Distance moved is an estimate of the distance you have covered based on the steps you take." }
```
*Also a copy issue: "Distance" capitalised mid-sentence.*

## BE-11 — [P3] Adherence-activity question and answers are English
**Endpoint:** `GET /vantagefit/api/v1/configuration` · `adherenceActivities[]`
```json
"title":    "Did you go for a morning walk today?"
"subtitle": "A simple walk can energize your body and clear your mind."
"options":  [ { "displayText": "No" }, { "displayText": "Yes" } ]
```
**Source of B26** — now confirmed in the body rather than inferred.

## BE-12 — [P3] Health-risk status value is English
```json
"hra": { "score": 55, "status": "Below Average" }
```

## BE-13 — [P2] Partner / Events section headings and subtitles are English
```json
"sponsoredLinks": { "heading": "Partner Offerings",
                    "subtitle": "To take care of your comprehensive wellness needs" }
"otherLinks": [ { "heading": "Upcoming Events",
                  "subtitle": "See what wellness activities are happening in your company" },
                { "heading": "Past Events",
                  "subtitle": "See past wellness activities in your company" } ]
```
**Note:** in **French** the same heading rendered as `Offres des partenaires` — so this endpoint **does**
localize for French but not Arabic. See **BE-16**.

## BE-14 — [P2] Malformed image paths are in the STORED DATA (= **B23**)
**Endpoint:** `GET /vantagefit/api/v1/content/top` · `image` field
```json
"image": "VantageFit/content_image/VantageFit/content_image/355_221322_1784701264.png.png"   ← doubled path + doubled extension
"image": "VantageFit/content_image/355_221322_1784463883.png.png"                            ← doubled extension
"image": "VantageFit/content_image/default.png"                                              ← the FALLBACK, which also 404s
```
**Proof this is backend, not frontend URL-building:** the malformation is inside the stored value.
**Measured effect:** 21 image requests 404 on a single Programs load; thumbnails render as black boxes.
**The fallback image itself 404s**, so there is no graceful degradation.
Also genuinely missing named assets, e.g. `bite-contents/sleep-management/sleep-management-01.png`.

## BE-15 — [P2] Weekly-task sentence has FOUR defects in one string (= **B27**)
Rendered from backend task data, **French** session:
> `Buvez au moins 2.0 L verres d'eau pendant 1 jours cette semaine`

1. **`2.0 L verres`** — nonsensical unit + noun ("2.0 L glasses"). Spanish showed the same shape: `fl oz vasos`
2. **`1 jours`** — pluralization (see BE-4)
3. **`2.0`** — **period decimal separator; French requires `2,0`**
4. **`Buvez`** — formal *vous* imperative, against the product's informal voice

**Note the contradiction with BE-7/BE-8:** here the backend *does* translate the words but not the number
format — the inverse of `Gagnez 10 000 Fit Points`, where the number format **was** correctly French.
**Number formatting is inconsistent between two backend-generated strings.**

## BE-16 — [P2] **RESOLVED BY DIRECT COMPARISON** — there are THREE distinct backend problems, not one

Captured the **same endpoints in Arabic and German** (2026-07-30). The result separates cleanly:

| Endpoint | **German** | **Arabic** | Verdict |
|---|---|---|---|
| `configuration` | ✅ **fully localized** — `Partner-Angebote`, `Kommende Veranstaltungen`, `Vergangene Veranstaltungen`, `hra.status: "Unterdurchschnittlich"` | ❌ all English | **Arabic coverage gap** |
| `challenge/ongoing/all` | ✅ **localized** — `Wöchentlicher Fortschritt`, `Wöchentlicher Rang`, `Gesamtrang`, `Nächster Meilenstein`, `E-Marathon-Herausforderung (endet in 21 Tagen)`, `0 Schritte abgeschlossen` | ❌ all English | **Arabic coverage gap** |
| **`today/overview`** | ❌ **ENGLISH** | ❌ English | 🔴 **endpoint has NO localization for ANY language** |

### The three problems, now precisely scoped

**16a — `today/overview` is not localized for any language.** Its response is **byte-identical English** in a
German session and an Arabic one: `"date":"Today, 30 Jul 2026"`, `dataType: Moved/Running/Cycling/Meals/
Water/Mood/Weight/Heart Rate`, `"value":"0.0 mile"/"0.0 fl oz"/"0.0 cal"`,
`"subText":"You are currently in a caloric deficit"`, `distanceData.subtext`.
**This upgrades BE-6, BE-7, BE-8, BE-9 and BE-10 from "Arabic problem" to "every-language problem."**
German, French and all other locales are affected. **Highest-value backend fix.**

**16b — Arabic has no backend translations at all.** Endpoints that are fully localized for German return
100 % English for Arabic. Arabic is live and selectable in the profile, so an Arabic user gets an
English backend everywhere.

**16c — `"Week 1"` is a specific missing key, not a coverage gap.** In the **German** `challenge/ongoing/all`
response, **9 of 10 fields are correctly German and `"subtitle":"Week 1"` is still English.** German has full
coverage on that endpoint, so this is one untranslated string, not a missing language.
**This definitively confirms B4 as a backend defect** (we had filed it frontend).

### Two more findings from the German comparison

**Pluralization is broken in every language** (BE-4 extended): English `(ends in 1 days)` ·
French `pendant 1 jours` · **German `(endet in 1 Tagen)`** — should be `1 Tag`. One backend template with no
plural rule, affecting all locales.

**B12 (formal/informal register) has a BACKEND source.** The German `configuration` response is **formal**:
`Um **Ihre** umfassenden Wellness-Bedürfnisse zu erfüllen` · `**Sehen Sie**, welche Wellness-Aktivitäten…` ·
`**Sehen Sie** vergangene Wellness-Aktivitäten…`. The product voice is informal *du*.
**B12 was filed as frontend copy; at least part of it is backend copy** and must be fixed there.

### Caveat
`adherenceActivities` was **empty** in the German capture and had 2 entries in Arabic, so **BE-11 could not be
compared across languages**. The array appears time- or state-dependent, not language-dependent. Re-check when
populated.

## BE-17 — [P4] Duplicate adherence activities in the data
`adherenceActivities` contains **two** "Morning Walk" entries (`firebase_id` 49 and 54) with near-identical
titles — `"Did you go for a morning walk today?"` vs `"Did you go for a morning walk today"` (one missing its
question mark). Data-quality issue; a user sees the question twice.

## BE-18 — [P4] Typos in stored content-category names
`Excercise` (should be *Exercise*) · `Mindfuless` (should be *Mindfulness*) — visible as category headings.

---

## Summary

| Priority | Count | IDs |
|---|---:|---|
| **P2** | **14** | BE-1, BE-2*, BE-3, BE-6, BE-7, BE-8, BE-9, BE-13, BE-14†, BE-15, BE-16, **BE-20**, **BE-23**† |
| **P3** | **8** | BE-4, BE-10, BE-11, BE-12, **BE-19**, **BE-21**, **BE-22** |
| **P4** | **3** | BE-5, BE-17†, BE-18‡ |
| **Total** | **22** | |

\* BE-2 needs a design answer first · † backend but **not localization** (BE-14, BE-17, BE-23) ·
‡ BE-18 is **rendered-only, unverified**

## Four classifications this run CORRECTED
These were filed as frontend bugs and are provably backend — the strings are in the API body:
**B4** (Week 1) · **B17** (caloric deficit) · **B18** ("mile") · **B1** (dates, in part).

## The one-line ask for the backend team
1. Accept and honour a locale on every `/vantagefit/api/v1/*` call (BE-1).
2. Translate the UI text the backend owns — headings, labels, `textConfig`, status values, adherence Q&A.
3. Stop sending **pre-formatted** dates, numbers and units; send raw values plus a unit code so the client can
   format them per locale (BE-7, BE-8).
4. Fix the plural rule and the decimal separator in task templates (BE-4, BE-15).
5. Repair the stored image paths and the fallback asset (BE-14).

---

# VERIFICATION AUDIT — 2026-07-30 (asked: "are you sure these are all backend? did you check the APIs?")

Fair challenge. Audited every finding against the standard **"was this read from an API response body?"**

## Verification status

| Verified from a body | Count |
|---|---:|
| ✅ **Read directly from an API response body** | **17 of 18** |
| ⚠️ **Inferred from the rendered page only** | **1** (BE-18) |

**BE-15 was the weakest item and is now VERIFIED.** Previously only seen rendered; now read from
`GET /vantagefit/api/v1/challenge/info?id=25423` in a **German** session:
```json
"task_title": "Trinken Sie an 7 Tagen in dieser Woche mindestens 67.6 fl oz Gläser Wasser."
```
Four defects in one backend string, all confirmed in the payload: **`67.6 fl oz Gläser`** (nonsensical
unit + noun) · **imperial unit in a German locale** · **`67.6` period decimal where German requires `67,6`** ·
**`Trinken Sie`** formal register.

**BE-18 (`Excercise` / `Mindfuless` typos) remains rendered-only.** The category names come from
`content/byCategoryName`, whose body I have **not** read. **Treat BE-18 as unverified** until it is.

## ⚠️ Three findings are BACKEND but NOT LOCALIZATION — reclassify before filing

These belong to the backend team but do not belong in a *localization* ticket:

| ID | What it actually is |
|---|---|
| **BE-14** | Malformed stored image paths / 404s → **asset & data integrity**, language-independent |
| **BE-17** | Duplicate adherence activities → **data quality** |
| **BE-18** | Stored typos → **content quality** (and unverified) |

## ⚠️ BE-2 needs a design answer before it is called a defect
`content/top` → `textConfig.general.heading` etc. may be **intentionally tenant-configurable** content that the
client is meant to render as-is. If so the defect is not "the backend sends English" but **"configurable
strings have no localization mechanism"** — a design gap, not a bug. **Ask before filing.**

---

# NEW findings from the German `challenge/info` body

## BE-19 — [P3] Mixed-language date **inside one backend string**
```json
"caption": "Endet am Aug 17, 2026 11:59 PM"
```
German prefix + **English month `Aug`** + **12-hour `11:59 PM`** in a German locale (German uses 24h).
**This is B1's backend source, proven in a German session** — so B1 is not Arabic-specific.

## BE-20 — [P2] Number formatting is INCONSISTENT WITHIN THE SAME RESPONSE
Same payload, same language:
```json
"footnote":   "Verdienen Sie 7.000 Fit-Punkte"                      ← ✅ correct German (period thousands)
"task_title": "... mindestens 67.6 fl oz Gläser Wasser."            ← ❌ wrong (period decimal; needs 67,6)
```
**The backend has correct locale number formatting in one code path and not in another.** This is the
strongest single argument that formatting should not be done server-side at all — send raw values.

## BE-21 — [P3] `Leaderboard` and `SCORE` are English in a German session
```json
"layoutType":"leaderboard", "info": { "title": "Leaderboard" }
"progressCaption": "SCORE"
```
Confirms the rendered `Leaderboard` / `You` finding as **backend**, on an endpoint that is otherwise
well-translated (`Wöchentlicher Rang`, `Thema der Woche`, `Fortschritt der Herausforderung`, `2 Wochen 4 Tage`,
`Wöchentlich` / `Gesamt`).

## BE-22 — [P3] `"Week 1"` untranslated on a SECOND endpoint
`challenge/info` → `layout[0].info.subtitle: "Week 1"`, while the same object has
`"caption":"Thema der Woche"` and `weekInfo.title:"Woche"` correctly in German.
**So the German word for "week" exists in this very response.** BE-16c confirmed twice, on two endpoints.

## 🔴 BE-23 — [P2] **NOT localization** — leaderboard pagination URLs point to `localhost:9050`
```json
"value": { "next": "localhost:9050/vantagefit/api/v1/challenge/info?id=25423&page=1",
           "last": "localhost:9050/vantagefit/api/v1/challenge/info?id=25423&page=1" }
```
A **developer-machine URL leaked into a UAT API response.** Any client following these links breaks, so
leaderboard pagination cannot work. Found incidentally during the localization audit.
**Flag to the backend team immediately — this is unrelated to localization and probably affects production.**

---

# PER-LANGUAGE MATRIX — 3 of 16 languages captured (ar · de · fr)

Each language requires a **profile switch + full re-login** to change what the API returns. There is **no
shortcut**: the API accepts no locale header (BE-1), and authenticated calls cannot be replayed manually —
auth uses an **HttpOnly cookie**, so a hand-built `fetch` gets 401 even with the session token. *(Attempted
and confirmed 2026-07-30.)*

## The matrix

| Field / endpoint | **Arabic** | **German** | **French** | Verdict |
|---|---|---|---|---|
| `challenge/*` → `rankTitle`, `progressTitle` | ❌ English | ✅ `Wöchentlicher Rang` | ✅ French | **Arabic coverage gap** |
| `configuration` → headings, `hra.status` | ❌ English | ✅ `Partner-Angebote`, `Unterdurchschnittlich` | ✅ `Offres des partenaires` | **Arabic coverage gap** |
| **`today/overview` → everything** | ❌ English | ❌ **English** | ❌ **English** | 🔴 **no localization for ANY language** |
| **`subtitle: "Week 1"`** | ❌ English | ❌ **English** | ❌ **English** | 🔴 **missing key in ALL 3** |
| **`Leaderboard` / `SCORE`** | ❌ English | ❌ **English** | ❌ **English** | 🔴 **missing key in ALL 3** |
| Water task wording | ❌ English | `67.6 fl oz Gläser Wasser` | `67.6 fl oz verres d'eau` | 🔴 **nonsensical in both translated languages** |
| Water task **decimal** | — | ❌ `67.6` (needs `67,6`) | ❌ `67.6` (needs `67,6`) | 🔴 **wrong in both** |
| Points **thousands** | — | ✅ `7.000` | ✅ `7 000` | ✅ **correct in both** |
| Plural "1 day" | `1 days` ❌ | `1 Tagen` ❌ | `1 jours` ❌ | 🔴 **wrong in ALL 3** |
| Formal register | — | ❌ `Trinken Sie`, `Ihre` | ❌ `Buvez`, `Votre` | 🔴 **formal in both** |

## What the three languages establish

**Confirmed language-independent** (present in every language tested — these are the priority fixes):
**BE-16a** `today/overview` unlocalized · **BE-22** `"Week 1"` · **BE-21** `Leaderboard`/`SCORE` ·
**BE-4** plural rule · **BE-15** water-task wording and decimal separator · **BE-12** formal register ·
**BE-20** number-format inconsistency *(points correct, task decimal wrong — in the very same payload,
in both de and fr)*.

**Confirmed Arabic-only** (BE-16b): the coverage gap. Endpoints that are fully de/fr return 100 % English
for Arabic.

## Remaining 13 languages — recommendation rather than a blanket sweep

The pattern is now **stable across 3 languages** (2 with backend coverage, 1 without), and every
language-independent defect above has been confirmed **3 times**. Additional languages would add **coverage**
information only, not new defect classes.

**Worth capturing (4):** **es** and **pt** — the remaining locales with a wired frontend dictionary, so the
ones the product claims to support; plus **pt-BR** / **pt-PT** as one variant check.

**Low value (9):** Chinese Simplified · Dutch · French Canada · Italian · Korean · Russian · Vietnamese ·
Hungarian · Japanese. None has a wired frontend dictionary, so "no backend coverage either" is the expected
and near-certain result. **One spot-check of a single unwired language would confirm the whole group.**

**Cost, stated honestly:** ~8–10 tool interactions per language for the switch and re-login alone, before any
capture. 13 languages ≈ 120+ interactions.

## Status
**3 of 16 captured. Every defect class is confirmed; only per-language coverage remains open.**
