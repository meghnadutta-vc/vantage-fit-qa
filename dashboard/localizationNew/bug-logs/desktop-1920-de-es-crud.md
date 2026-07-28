# Desktop (1920×1080) UI Sweep + CRUD — German & Spanish (Run 10, 2026-07-28)

**Viewport 1920×1080** — the most common real desktop resolution, and the one gap left by Runs 5–9
(which covered 1024 / 1366 / 1440 only). Both languages, all modules. Tenant India / 355 (UAT).

**Overflow is classified this time**, which matters for triage:
- **CLIP** — `overflow-x: hidden|clip` → text is cut off and unreadable. **Real bug.**
- **SPILL** — `overflow-x: visible` → text escapes and collides with neighbours. **Real bug.**
- **SCROLL** — `overflow-x: auto|scroll` → wide data tables that are *meant* to scroll. **Not a bug**, and
  excluded from the counts below so it stops generating noise.

---

## Headline: the desktop picture is very different from the 1024 picture

At 1024 I found breakage in 15 of 17 modules. **At 1920, across 23 surfaces per language, only three
components break — and every one of them is a fixed-width box.**

| Component | Box | German | Spanish | Kind |
|---|---|---|---|---|
| `.notif-title` (Publish Notifications) | **150px** | +3px | **+8px** | CLIP |
| Report column-selector chip | **150px** | +31px / +48px | +31px / +48px | CLIP |
| Wellness Leagues filter chips | **110px / 100px** | +62px / +23px | +58px / +11px | CLIP |

Everything else — Overview, Create Challenge, Manage Challenges, Past Challenges, Events, Send Custom
Email, Settings, Add Employees, Preview Emails, Content Library, Create Content, Create Event, Create
Announcement, Wellness Score, Upload Points, all 6 Reports, the wizard steps — measured **0 breaks in both
languages**.

### What this means for the existing bug pile
**OV#8, OV#9, OV#11, EV#4, SET#3, MGC#3, CC#6 and PN#1's `.two-column-layout` do NOT reproduce at 1920.**
They are genuine bugs at 1024/1366/1440, but they are **viewport-dependent, not desktop-facing**. So the
large P3 layout pile from Run 5 should be triaged as *"affects small laptops and split-screen"*, not
*"affects everyone"* — that is a materially lower priority than the raw count suggested.

**The exception is the fixed-width group above.** A 150px box does not grow with the viewport, so those
three clip at **every** resolution. They are the only width-independent layout defects in the engagement,
and they are the ones actually worth fixing first.

**EV#4 re-rated down.** I had flagged it as a possible functional P2 (German event tabs clipped +291px, no
pagination → tabs maybe unreachable). At 1920 the tabs fit with room to spare, so German admins on a normal
desktop *can* reach them. It stays a P3 confined to narrow viewports — **not** the P2 I suspected.

**Broken images are resolution-independent**, as expected for a CDN URL defect: 5 on Manage Challenges,
12 on Events, 1 on Past Challenges — identical at 1920 in both languages.

---

## CRUD results

### ✅ G5 — comma-decimal input: CONFIRMED DEFECT (the P1 lead)
**Settings → Maximale Teamgröße**, German locale, real keystrokes:

| Typed | Stored value | `validity.valid` | `badInput` | Error shown |
|---|---|---|---|---|
| `12,5` | **`125`** | **true** | false | **none** |

The locale decimal separator is **silently discarded** and the remaining digits are concatenated, so the
value becomes **ten times the intent** with no warning and no validation failure. A second observation made
it worse: typing `12,5` into a field already containing `500` produced **`500125`** — input is appended, not
replaced, and the comma vanished again.

**Expected:** either accept `12,5` as 12.5 (locale-aware parsing) or reject it with a localized validation
message.
**Actual:** comma dropped, value silently wrong, form reports valid.
**Severity: P2 as measured, P1 candidate pending one more test.** On this particular field the impact is
bounded — team size is an integer, so a decimal is user error anyway, and `max=500` clamps it. **The
mechanism is confirmed; what is not yet proven is the impact on a field where decimals are legitimate**
(points values, distance/step targets). I attempted to reach the challenge task-target field to test
exactly that and could not — targets only appear after an activity is added to the week (see gaps below).
That single test is what would settle P1 vs P2, and it applies to **French and Spanish too**, which also
use a comma decimal separator.

### ✅ UPDATE — works, correctly localized, reverted
- **Settings:** changed team size, **`beforeunload` guard fired** on navigation away (unsaved-changes
  protection exists and works). Discarded; verified back to `500`. Nothing persisted.
- **Preview Emails:** toggled *Wöchentliche Zusammenfassung* off → counter updated to a correctly
  localized, correctly pluralized **"8 von 9 aktiviert"** → toggled back → **"9 von 9 aktiviert"**.
  Org config restored. **No toast fired on toggle** (contrary to the assumption that Preview-Emails saves
  always toast — the toast appears on explicit save, not on toggle).

### ✅ CREATE — validation and wizard verified; publish deliberately NOT executed
Walked the Custom Challenge wizard: **Info → Dauer → Zielgruppe → Konfiguration** (4 of 5 steps), all at
1920, all **0 layout breaks**.

- **F3 validation confirmed:** `Weiter` is `aria-disabled="true"` until the required Challenge-Name is
  filled, then flips to `false`. Preventive gating, no inline error strings — as expected for this design
  system.
- **I did not publish.** The audience step resolved to **960 real UAT users**, and I could not narrow it to
  a single test account within the run (the user-search did not filter the loaded list). Publishing a live
  challenge plus notifications to 960 real people exceeds the blast-radius rule, so I stopped at the
  configuration step. **Verified afterwards that nothing persisted** — active challenges still **103**, no
  draft created, no new test-data debt.

### ❌ DELETE — not available in the UI at all
Challenge cards expose only **Ansehen** and **Verwalten**. There is **no delete control**, which confirms
the Coverage Matrix's "no delete UI" and explains why challenge **25441** is permanent debt (**G24**).
**This is an Enhancement/gap, not a defect** — but it means every challenge created during testing is
unremovable, and QA test data accumulates in the tenant forever. Worth raising with product.

---

## New localization findings from the CRUD walk

### 1. CC#2 confirmed in German at 1920 — date picker fully English
Calendar header **`JUL 2026`**; weekday headers **`Monday…Sunday`** with initials **`M T W T F S S`**.
German should be `Juli 2026` and `Mo Di Mi Do Fr Sa So` — **even the initials are wrong**, since German
weekday initials are M/D/M/D/F/S/S. Evidence: `evidence/de_1920_datepicker_english_calendar.png`

### 2. Date input format is not German
The field accepts and displays **`30/07/2026`** with placeholder `DD/MM/YYYY`. German convention is
**`30.07.2026`** (periods). The format is day-first so it is not US format either — it is UK/Indian format
applied to a German locale.

### 3. CC#3 confirmed at 1920, with a fuller inventory
On the Zielgruppe step, labels are German (*Abteilung*, *Land/Region*, *Geschlecht*, *Altersgruppe*) but:
- the operator **`is in`** renders English **4×**
- **`(+124 others)`**, `(+14 others)`, `(+3 others)`, `(+5 others)` — English **4×**
- inside the dropdown, **`All`** and **`Undisclosed`** are English (should be *Alle* / *Nicht angegeben*)

### 4. [BE] Activity master list — correctly identified as NOT a bug
All 21 activities render English (*Steps, Walking/Running, Water Intake, Mood-O-Meter, Track Sleep,
Active Minutes*…). Dictionary check: **20 of 21 have no key in any language**, so they are backend master
data from `/vantagefit/api/v1/challenge/multiweek/config` → **[BE], expected English**, not a defect.
**One nuance, stated as a nuance:** `"Steps"` *does* have German `"Schritte"` in the dictionary — but under
`reportCols.steps` / `contest.steps`, i.e. for the report column and contest contexts, not for this list.
So it does **not** prove the activity list should localize. I attempted to confirm the source by re-fetching
the endpoint and got **401** (the app supplies auth headers a bare fetch doesn't), so the source is inferred
from the endpoint plus dictionary absence rather than read from the body. **Needs Product Confirmation**
whether activity names are in scope for localization at all.

### 5. G13 terminology split — CONFIRMED with hard evidence (was a prediction)
The glossary pass hasn't formally run, but this run caught the split I predicted:

| Surface | Term used |
|---|---|
| Preview Emails | **Herausforderung**serinnerung, **Herausforderung**sstart, Abschluss der **Herausforderung** |
| Sidebar + wizard | Aktive **Challenges**, **Challenge** erstellen, **Challenge**-Name, **Challenge**-Slogan |

Same concept, two different words, in the same product, both visible in one session. This is a real
terminology defect for the glossary pass, not a judgment call.

### 6. Register defect — formal *Sie* on the Create Challenge landing
The heading reads **"Erstellen *Sie Ihre* eigenen Neuen Challenges"** — formal register, against Vantage
Fit's informal *du* voice (the same defect class as **B12** on the employee web). Also *"Neuen Challenges"*
is oddly inflected/capitalized for a heading. **First concrete dashboard instance of the register split.**

---

## Coverage

| | German | Spanish |
|---|---|---|
| Modules swept @1920 | **23 surfaces** | **23 surfaces** |
| Wizard steps @1920 | 4 of 5 | — (German only) |
| CRUD | Create (partial), Update ×2, Delete (unavailable) | — |

Sidebar navigation: all 24 labels correctly localized in both languages.

---

## What was NOT done

- [ ] **Publish a challenge** — withheld deliberately (960-user blast radius). Needs either a way to scope
      the audience to one test account, or explicit approval to publish to that group.
- [ ] **G5 on a decimal-legitimate field** (challenge task target / Upload Points value). **This is the one
      test that decides whether G5 is P1 or P2** — the target field requires adding an activity to the week
      first, which I ran out of room to do.
- [ ] Wizard **step 5 (Review)** never reached.
- [ ] **CSV upload CRUD** (Add Employees / Upload Points) — G6 non-ASCII + semicolon delimiter still untested.
- [ ] Spanish CRUD — all CRUD above was German only.
- [ ] Toast localization on **create/publish** flows at 1920 (no create was completed).
- [ ] 768 / 375 (mobile/tablet) still untested in any language.
- [ ] Health Insights — ⛔ external iframe.
