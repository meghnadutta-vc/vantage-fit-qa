# Vantage Fit Web — BACKEND localization bugs

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
| **P2** | **11** | BE-1, BE-2, BE-3, BE-6, BE-7, BE-8, BE-9, BE-13, BE-14, BE-15, BE-16 |
| **P3** | **4** | BE-4, BE-10, BE-11, BE-12 |
| **P4** | **3** | BE-5, BE-17, BE-18 |
| **Total** | **18** | |

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
