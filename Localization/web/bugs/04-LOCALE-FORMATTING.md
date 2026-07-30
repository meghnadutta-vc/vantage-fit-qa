# 04 — LOCALE FORMATTING

Dates, times, numbers, decimal separators, units, currency.

**Most of this file traces to one locale-unaware formatting layer, exactly as on the admin dashboard — but
with one crucial difference: here a large share of the formatting is done by the BACKEND**, which sends values
pre-formatted. That changes who owns the fix, so read the ownership column carefully.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01 — fix there first
`B1` — the date/time formatter, **P2**.

---

## 🔴 B1 — [P2] Dates and times render in English format and English words in every language · [FE + BE]

The single highest-leverage formatting fix, and the one with the widest reach.

### Confirmed instances

| Rendered | Language | Should be | Where |
|---|---|---|---|
| **`Thursday, 30 July 2026`** | fr | `jeudi 30 juillet 2026` | Summary header |
| **`20th Feb 2026`** | fr | `20 février 2026` | "Votre dernier badge" card — **English ordinal date inside a French card** |
| **`2:33 PM`** | fr | `14:33` — France uses a 24-hour clock | Log Activity form |
| **`Aktualisiert am 14 Jul 2025`** | de | `… 14. Juli 2025` | **Mixed language *within one phrase*** |
| **`Mis à jour le 17 Jan 2024`** | fr | `… 17 janvier 2024` | Same pattern, French |
| **`07 Oct 2025 - 15 Sep 2025`** | all | — | see **B32** — this range is also *backwards* |

**The `Aktualisiert am 14 Jul 2025` case is the most diagnostic:** the surrounding words localize **perfectly**
while the month never does. That proves whatever renders that element *can* localize, and isolates the defect
to the **date layer**. Identical reasoning to the dashboard's U7#3, and it held there too.

### Ownership is split — this is not one fix

| Part | Owner | Evidence |
|---|---|---|
| Client-rendered dates | **frontend** | rendered from data, formatted locally |
| **`BE-8`** — the backend sends a **pre-formatted English date** | **backend** | confirmed in the API response body |
| **`BE-19`** — a **mixed-language date inside one backend string** | **backend** | the string arrives already broken |

**So B1 cannot be closed by a frontend change alone.** Anyone estimating it needs `11-BACKEND.md` open
alongside.

### Judgment stated openly
`2:33 PM` (12-hour time) and `5.0` (decimal point where French needs `5,0`) were **folded into B1** rather than
given their own IDs, because they are the same locale-unaware formatting layer and B1 is already P2. **They
split out cleanly** if the team prefers one ticket per symptom — the underlying observations are recorded
independently in `bug-log.md` addendum 18.

---

## B7 — [P3] Trend-chart weekday axis not localized · [FE]

Renders `S M T W T F` — English weekday initials. German needs `M D M D F S S`, French `L M M J V S D`.

**Note the initials are not just untranslated but structurally wrong for other languages** — several languages
have repeating initials and need a different disambiguation convention. This is not a string swap.

---

## B6 — [P3] Measurement unit words stay English · [FE + BE]

`mins` · `sec` · `hrs` · `/day` · `g/dL`. French session shows `0/32 mins`; French uses `min`.

**Also `BE-7`:** units arrive from the backend **pre-formatted and imperial**. So there are two problems
stacked — the *word* is English **and** the *system* is wrong for a metric locale.

---

## B18 — [P3] The word `mile` untranslated in Diary's Distance section · [FE + BE] · = **BE-10**

Same double problem as B6: English word **and** imperial unit for a metric locale. German confirmed.

---

## B28 — [P3] Log Water `1 glass = 250 ml` does not convert when switching to fl oz · [FE]

The **value and the slider do convert**; the **helper label stays metric**. So the modal shows an imperial
value beside a metric explanation.

### ✅ Important contrast found 2026-07-30 — this is a LOCAL defect, not a platform gap

The **Log Activity** km/mi toggle was tested with the same method and **passes**:

| State | Distance | Calories | Estimate |
|---|---|---|---|
| km | **5.0** | 384 | `Estimated · 268–500 kcal` |
| → mi | **3.1** | 384 | `Estimated · 268–500 kcal` |

5.0 km = 3.107 mi — **correct**. Calories correctly unchanged (same distance, different display unit).

**Why this matters for triage:** the app **can** convert units correctly; one modal does it right and another
does it partially. That makes B28 **cheaper and more contained** than if conversion were missing platform-wide,
and it gives the developer a working in-codebase example to copy.

**Standing rule this test came from:** *when a modal has a unit toggle, verify every value **and** every label
converts — partial conversion is a data-integrity class of bug, not cosmetic.*

---

## B8 — [P3] `Active Minutes` capitalized inconsistently · **[BE] — reclassified 2026-07-30**

**Proven backend.** Three fields, **one `app/home` response**, French session:

| JSON path | Value |
|---|---|
| `progressUI.metrics[1].displayTitle` | **`Minutes Actives`** ← wrong |
| `progressUI.metrics[1].legend` | **`Minutes Actives`** ← wrong |
| `trends.snippets[1].title` | **`Minutes actives`** ← **correct** |

Same term, two casings, same payload. The frontend receives both identically — **it cannot be a client-side
bug**, and it cannot be "the backend can't do casing" because one of the three is right.

Confirmed instance of **BE-5**. Canonical entry moved to `11-BACKEND.md`.

---

## B32 — [P3] A past challenge shows an end date BEFORE its start date · [FE-BE TBD]

`07 Oct 2025 - 15 Sep 2025`. **This is not a formatting bug** — it is either bad stored data or a
start/end swap in rendering. Listed here because it surfaced during date testing.

**Source unconfirmed.** Needs a call: is the stored record wrong, or is the frontend transposing the fields?
Those are different fixes with different owners.

---

## Not tested — stated so nobody assumes it passed

| Dimension | Status |
|---|---|
| **Large-number grouping** (`1.234.567` vs `1,234,567`) | **never verified** — no seeded data produces values that large |
| **Currency** | **never verified on this surface** — no currency-bearing screen was reached. The dashboard found `$0` on an India tenant; unknown here |
| **Timezone** | **0 of 5 modules.** Needs an account on a non-IST timezone |
| **Percentages** | not systematically checked |

---

# ═══ BACKEND ═══

**Formatting is where this surface's backend involvement is heaviest.** Full detail in `11-BACKEND.md`:

| Finding | Why it belongs to backend |
|---|---|
| **BE-7** — units are English **and imperial**, served **pre-formatted** | The frontend receives a finished string; it cannot re-unit it |
| **BE-8** — the backend sends a **pre-formatted English date** | Same — arrives already formatted |
| **BE-19** — a **mixed-language date inside one backend string** | The string is broken before it reaches the client |
| **BE-20** — **number formatting is inconsistent *within a single response*** | `7.000` formatted correctly alongside `67.6` formatted wrongly, **in the same payload**. Decisive: this cannot be a client-side bug |

**BE-20 is the strongest single piece of evidence in the whole formatting story.** Two numbers in one response,
one right and one wrong, proves the server is doing locale-aware formatting **inconsistently** — so
"the frontend should just format it" is not an available answer for these fields.

**On BE-1 / B38 — corrected 2026-07-30.** It was previously written here that the backend "is formatting for a
locale it was never told". **That is wrong.** Live capture shows the request carries no locale **and the response
comes back correctly French** — the backend resolves the language **server-side, from the account.**

So these formatting defects are **not** explained away by the missing header. The backend **knows** the locale
and still formats inconsistently — which is exactly what **BE-20** shows within a single payload. **Fixing B38
will not fix this group.** They are independent, and this group is genuinely a backend formatting defect.
</content>
