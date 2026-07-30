# 02 — UNTRANSLATED / NOT LOCALIZED

English text on a screen the user has set to another language. **The largest defect group here, as on the
dashboard — but for a completely different reason.**

---

## ⚠️ Read this before using any of the entries below

On the **admin dashboard**, every bug in this category was a **wire-up gap**: 18 complete dictionaries of 991
keys existed, and the component simply rendered a literal instead of the key. Cheap, incremental fixes.

**On this surface there is no key to wire.** The Fit module has **no translation mechanism at all** (**B39**,
`01-P1-P2-CRITICAL.md`) and its strings are compiled into the bundle as static template literals.

**So every entry below is one of three things:**

| Class | Meaning | Fix |
|---|---|---|
| **Not externalised** | A hardcoded literal in the Fit bundle. **Most entries here.** | Only fixable as part of B39 |
| **Backend-served English** | The server sent English because the frontend never told it the locale (**B38**) | Fix B38, then `11-BACKEND.md` |
| **Authored content** | A challenge name, post title, username, library title | **Expected — not a bug** |

**Do not estimate these individually.** Counting them as 15 separate fixes badly misrepresents the work.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first

`B39` (root cause) · `B3` · `B4` · `B5` · `B16` · `B17` · `B19` · `B20` — all **P2**.

---

## The full English-chrome inventory (French session, `<html lang="fr">`)

This is what a French-speaking employee actually sees. Captured 2026-07-30.

**Navigation and primary actions — 11 strings:**
`Summary` · `Challenges` · `Programs` · `Community` · `Add` · `Open Diary` · `View Trends` · `View challenge` ·
`Previous challenge` · `Next challenge` · `Need Help with Vantage Fit?`

**Card and section headings:**
`Snapshot` · `Vitals` · `Health` · `Highlights` · `Featured Content` · `Health bites` · `Calorie Ledger` ·
`Food Log` · `Wellness Score`

**Quick Add menu — 12 of 12 English:**
`Workout` · `Mindfulness` · `Log Diary` · `Track Habits` · `Sync Steps History` · `Track on app` ·
`Measure Heart Rate` · `Track Squats` · `Log Activity` · `Start Outdoor Workout` · `Start 7-Minute Workout`

**Log Activity picker — 4 section headers:**
`Well Being` · `Most Popular` · `Cardiovascular Activities` · `Sports`

**Activity form (Hiking) — 10 labels:**
`Date` · `Today` · `Time` · `Duration` · `Distance` · `Calories` · `Estimated · 268–500 kcal` ·
`Convert this activity to` · `Active Minutes` · `Log activity`

**Subtitles:** `Compete with peers & colleagues, track your tasks.` · `15-30 sec tips for a healthier you.`

All confirmed present in the bundle as literals where probed. **This inventory is the visible face of B39** —
it is one defect, not fifty.

---

## Individual entries

### B3 — `Challenges` nav tab untranslated in German · P2
**The decisive detail:** German body text on the same screen says **`Herausforderung`**. So the concept *does*
have a German rendering somewhere — it comes from the API — while the **nav tab** is a bundle literal. One
concept, two languages, one screen. This was the first clue to B39 and nobody recognised it at the time.

*(Historical note: this bug was cited as "B5" across 6 files for several days. Its real ID is B3 — B5 is the
Highlights social strings, unrelated. Verify IDs against `bug-log.md` before citing.)*

### B4 — `Week 1` untranslated in every language · P2
Also **BE-22** — confirmed untranslated on a **second endpoint**, so it is backend-served in at least one path.
Note the dashboard found German rendering `Woche 1` correctly in one place and `Week 1` in another, i.e. the
translation exists there. **Here it does not.**

### B5 — Highlights social strings untranslated · P2
`Posted by` · `Likes` · `Comments` · `2 days ago`. The relative-time string is the interesting one — it needs a
formatter, not just a string, so it will not be fixed by translation alone.

### B6 — Measurement unit words stay English · P3
`mins` · `sec` · `hrs` · `/day`. In the French session `0/32 mins` renders — French uses `min`. Also
**BE-7**: units arrive from the backend **pre-formatted and imperial**, which means the frontend cannot fix
this alone.

### B9 — `Wellness Score` stays English · P4 · **judgment call**
**Our opinion: this is probably correct as-is** and should not be filed as a defect until product rules on it.
`Wellness Score` is plausibly a **brand/product term**, like "Vantage Points". Brand terms conventionally stay
as authored. **Needs one product answer**, then it moves to `09-NOT-A-BUG.md` or becomes a real bug. Canonical
entry: `08-ENHANCEMENTS.md`.

### B13 — `Written By` untranslated in bite-size content detail · P3
Programs module. Confirmed **de + es**.

### B16 — Community chrome 0% localized · P2 · **the deterministic case**
Confirmed **de + es**. Community is the one module where the failure is **total and reproducible** rather than
intermittent, which makes it the best place to verify a B39 fix.
**The reverse signal lives here:** a single correctly-translated string (the empty-state text) stranded in an
otherwise all-English view. That lone string proves the **session** language is fine and the route's own chrome
is the problem — the reasoning was sound, it just stopped one level short of B39.

### B17 — `You are currently in a caloric deficit` untranslated · P2 · = **BE-9**
Diary, German. **Server-rendered** — confirmed in the API body. Frontend cannot fix this.

### B18 — The word `mile` untranslated in Diary's Distance section · P3 · = **BE-10**
Two problems in one: the **word** is English **and** the **unit is imperial** for a metric locale.

### B19 — Trends page mostly unlocalized · P2
And **inconsistent within itself**: Spanish also drags the nav down while German does not.

### B20 — Diary chrome + nav regress to English in Spanish · P2
**The single most important data point about method on this surface.** Diary is the **best**-localized screen
in German and roughly **90% English in Spanish, including the nav bar**. A German-only pass would have recorded
Diary as a success story.

### B21 — Spanish renders "challenge" two ways · P3
Nav `Retos` vs body `Desafío`. **Mechanically different from B3** — B3 is one language vs another (missing
translation); B21 is **two different words within the same language** (a glossary gap). Both need fixing, by
different owners: B3 by engineering, B21 by the terminology owner.

### B26 — Adherence answer option `Yes` untranslated · P3 · = **BE-11**
Should be `Sí`. Comes from the `configuration` API as **data**, not chrome — so this is a backend content fix.

### B34 — Language dropdown lists all option names in English · P4 · **judgment call**
Regardless of the current UI language. **Our opinion: this is defensible and probably correct.** Language
pickers conventionally show either endonyms (each language in its own name) or a stable reference language.
Independent of B39. Canonical entry: `08-ENHANCEMENTS.md`.

---

## Expected to stay as authored — NOT bugs

Recorded so nobody files them:

- **Brand tokens** — `Vantage Fit`, `Vantage Points`
- **Challenge names** — `QA-BOT Custom 0721`, `Marathon Mania`
- **Community post titles** — `Q3 Wellness Program — Now Live`
- **Usernames** — `Anjan Pathak`
- **Library content titles** — `Managing Workplace Stress: A Practical Guide`, `Crossfit 101`
- **Custom activity names** — `Running Test`, `New Custom Activity`

**Method warning that caught a real false positive:** an English-month regex will match **authored data** —
`Announcement 17 Sep` is a *challenge name*, not a UI date. **Always diff against the English-baseline content
list before flagging any date or mixed-language finding.**

---

# ═══ BACKEND ═══

Every backend-served English string is in **`11-BACKEND.md`** (BE-1–BE-23). The ones cross-referenced above:
**BE-7** (units), **BE-9** (= B17), **BE-10** (= B18), **BE-11** (= B26), **BE-22** (= B4).

**The gate on all of them is BE-1 / B38** — the frontend sends no locale, so the backend cannot localize even
where it is capable of it. **Fix that first or none of the backend fixes can be verified.**
</content>
