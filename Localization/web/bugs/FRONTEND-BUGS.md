# Vantage Fit Web — FRONTEND localization bugs

> **📁 SUPERSEDED as a standalone report — kept for history.**
> Its content is now distributed across the categorised folder: the P1/P2 tiers became
> [`01-P1-P2-CRITICAL.md`](01-P1-P2-CRITICAL.md), the PASSES section became
> [`09-NOT-A-BUG.md`](09-NOT-A-BUG.md), and the untestable section became
> [`10-BLOCKED-NEEDS-DECISION.md`](10-BLOCKED-NEEDS-DECISION.md).
> **It also predates B39**, so its framing of B33 as the root cause is out of date.
> **Start at [`00-INDEX.md`](00-INDEX.md).**


**Surface:** employee Fit web · `app.vantagecircle.co.in/ng/fit/*` · UAT
**Compiled:** 2026-07-30 · Source of record: `bug-log.md` · Backend counterpart: `BACKEND-BUGS.md`

> **How frontend was proven:** every string below appears in **no API response body** we inspected, so it must
> come from the frontend translation dictionary — which is not being served (**B33**). Endpoints checked:
> `configuration` · `today/overview` · `challenge/ongoing/all` · `content/top` · `content/byCategoryName`.

---

## 🔴 P1 — fix first

### B33 — The Fit translation file is not served; it returns the SPA homepage HTML
Full developer write-up with proof: **`../B33_DEVELOPER_ISSUE.md`**

| Path | Status | content-type | Bytes |
|---|---|---|---|
| `/ng/assets/i18n/de.json` (perks) | 200 | **application/json** — 1,472 keys ✅ | — |
| `/ng/assets/i18n/fit/de.json` | 200 | **text/html** ❌ | **115,655** |
| `/ng/assets/i18n/fit/en.json` | 200 | **text/html** ❌ | **115,655** |

Identical byte counts for de/en/fr prove one wrong document is served for every locale.
**Effect (German):** Programs **0 %** translated · Diary 3 % · Trends 5 % · Community 9 % · Summary 16 % ·
Challenges 20 %. **A regression** — strings that rendered translated on 24–28 July are in neither file today.
**Supersedes B10** (same defect, was filed P4).

**Confirmed frontend strings blocked by this** (absent from every API body):
`Diary` · `Snapshot` · `Calorie Ledger` · `Food Log` · `Intake` · `Bedtime` · `Wake up` · `Log Water` ·
`Log Sleep` · `Log Activity` · `Save` · `Update weight` · all four nav tabs · `Add` · `View Trends` ·
`Open Diary` · `View challenge` · `Leaderboard` · `You`.

---

## P2

| ID | Bug |
|---|---|
| **B35** | **Arabic/RTL: numbers, units and dates render in REVERSED visual order** — DOM is correct, painting is wrong (`unicode-bidi: normal`, no isolation). ~26 instances. `4 hrs 19 mins` → `hrs 19 mins 4`; `24 - 30 Jul` → `Jul 30 - 24`. **Invisible to text extraction.** Worst: every date range on the Past-challenges tab |
| **B2** | Language-change alert prints a literal `{language}` token. Confirmed from **German and French** sessions; only English interpolates |
| **B3** | Nav tabs untranslated — **all four** (`Summary/Challenges/Programs/Community`), wider than originally filed |
| **B5** | Highlights social strings untranslated (`Posted by`, `N Likes`, `N Comments`, relative time) |
| **B11** | Language preference not persisted across re-login. ⚠️ **Did NOT reproduce 2026-07-29** — re-verify before citing |
| **B12** | Formal/informal register mixing. Identical structural positions in de/es/fr: `Ihr neuestes Abzeichen` / `Su última insignia` / `Votre dernier badge` → strongly suggests one shared source string |
| **B16** | Community's own chrome 0 % localized; nav/footer regress on that route. Confirmed in 4 languages |
| **B19** | Trends chrome untranslated (`Week/Month/Year`, chart title, `Activity Details`) |
| **B20** | Diary chrome untranslated (language-dependent) |
| **B25** | Runtime language desync mid-session; also affects backend content queries. ⚠️ **Likely a symptom of B33** — re-derive after the fix |
| **B31** | Log Water submit with no amount **closes the dialog with zero feedback**. Toast absence **confirmed** (observer + 2.5 s wait) |
| **B38** | Frontend sends **no locale** to the Fit API — see `BACKEND-BUGS.md` **BE-1**. Listed here because the *request* is the frontend's responsibility |

## P3

| ID | Bug |
|---|---|
| **B6** | Units untranslated in FE-rendered text (`mins`, `sec`, `hrs`, `/day`) |
| **B7** | Weekday chart axis untranslated (`S M T W T F`) |
| **B8** | Casing inconsistency for one label (fr/pt) |
| **B13** | "Written By" untranslated in bite-size content |
| **B15** | CTA button overlaps body text in the bite-size intro (language-independent) |
| **B21** | Two different Spanish words for one concept — nav `Retos` vs body `Desafío` |
| **B22** | Metric-switcher pill overlaps its neighbour. **Measured: French 144px pill vs 100px segment = 44px** (worse than Spanish's 40px — a *shorter* translation overflows more) |
| **B28** | Log Water `1 glass = 250 ml` helper doesn't convert with the unit toggle. **Confirmed in en/es/fr → language-independent unit-conversion bug, not a translation defect** |
| **B29** | Challenge card overflows its box by 36px **at ≥1440 only** (fits at 1024/1366). **Reproduce at ≥1440 or it looks unreproducible.** Affects desktop users |
| **B30** | **Inconsistent dialog semantics across modals.** Log Sleep has `role=dialog` + `aria-modal` + name ✅; Log Activity has role+aria-modal but no name; **Log Water and Log Weight have none**. **Focus is not moved into any of the four.** The correct pattern already exists in the codebase |
| **B32** | Past challenge shows an end date **before** its start (`07 Oct 2025 - 15 Sep 2025`) · `[FE-BE TBD]` |
| **B36** | Custom controls have no accessible semantics — water ruler is a bare `DIV` with `role:(none)`, no `aria-valuenow`, no `tabindex`; sleep and activity steppers likewise. **Keyboard/screen-reader users cannot set these values** |
| **B37** | **7 elements fail WCAG AA contrast.** Worst is a **data value**: `-7,381` (calorie deficit) at **1.79:1** vs 4.5 required. Active nav pill 3.45:1 |

## P4 / judgment

| ID | Bug |
|---|---|
| **B9** | "Wellness Score" stays English — brand term? Needs product confirmation |
| **B10** | ⛔ **SUPERSEDED by B33** — same defect, was mis-rated P4 "infra" |
| **B34** | Language dropdown lists all 16 options in English regardless of UI language (endonyms?). Independent of B33 — it is in the perks app where the dictionary works |

---

## PASSES — recorded so they are not re-tested

| Area | Result |
|---|---|
| **RTL implementation** | ✅ `dir="rtl"`, layout mirrors, **RTL tab order correct**. **Opposite of the admin dashboard, where RTL is absent** |
| **Focus visibility** | ✅ real Tab gives `:focus-visible` with a `2px solid` outline. *(A programmatic `.focus()` probe wrongly suggested it was missing.)* |
| **Keyboard reachability** | ✅ 33 focusable, 1 unreachable |
| **Comma-decimal input** | ✅ **structurally impossible** — no free-text numeric field exists on any form. This was the dashboard's only P1 lead |
| **Derived-value recalculation** | ✅ Activity: duration 45→15 min recalculates calories 180→60 and the estimate range, all consistent at 4.0 kcal/min |
| **kg/lbs conversion** | ✅ correct (51.0 kg → 112.4 lbs, ruler switches to an lbs range) |
| **Number formatting (fr)** | ✅ `Gagnez 10 000 Fit Points` uses the French space separator |
| **Narrow widths** | ✅ 1024 and 1366 clean; no page-level horizontal overflow; layout reflows |
| **Arabic numerals** | ✅ consistent Western digits, no mixing within a string (the dashboard's AR#3 does **not** reproduce) |
| **Glyph rendering** | ✅ Latin and Arabic render with no tofu/mojibake |
| **`<html lang>`** | ✅ correct per locale (the dashboard has it stuck at `"en"`) |
| **Functional traversal** | ✅ all sub-tabs, challenge detail, both Programs sub-tabs, View-all modal, Trends ranges, all modals |

## Untestable / unresolved

- **F6 accented search** — ⛔ **no search input exists anywhere** on this surface (DOM-confirmed). N/A, not a gap.
- **`Quick add` / `+Add`** — did not open on **2 attempts across 2 sessions**, no menu/modal/URL change.
  **Needs a human click** before logging.
- **Screen-reader pass** with real assistive tech — never done.
