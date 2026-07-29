# UI-Break Sweep — Spanish (es) — Dashboard (Run 7, 2026-07-28)

**Method:** same corrected detector as the German sweep (`scrollWidth > clientWidth`, overflow-property
agnostic, with the fixed `vis()` helper that rejects ancestor-collapsed elements). Fresh route load per
measurement. **Widths 1024×768 and 1440×900.** Tenant India / 355 (UAT). `fit_lang = es`.

**Purpose:** with German done and English controls taken, Spanish is the third data point — it separates
"this container is too small for *any* non-English language" from "this is specifically German's length."

---

## The headline: OV#8 was two different bugs, and Spanish proved it

Measuring the At-a-Glance metric labels (`.item-header`) across three languages and two widths:

| Width | Box | English | Spanish | German |
|---|---|---|---|---|
| **1440** | 113px | 4/4 fit **exactly** (0px headroom) | 4/4 fit **exactly** (0px headroom) | **3/4 overflow** (+4 / +8 / +27) |
| **1024** | 44px | **4/4 overflow** (+5 / +18 / +18 / +4) | **4/4 overflow** (+27 / +18 / +21 / +27) | **3/4 overflow** (+73 / +77 / +96) |

**This splits OV#8 into two separate defects that I had conflated:**

- **OV#8a — localization, at normal desktop width (1440).** English *and* Spanish both fit in exactly
  113px; only **German** overflows and renders over the tile icon. This is the genuine localization bug and
  it is **German-specific**.
- **OV#8b — responsive, at 1024.** The container shrinks to 44px and **every language including English
  overflows.** Nothing to do with translation; the tile simply cannot hold any label at that width.

Practical consequence for triage: fixing the German strings alone would close OV#8a but leave OV#8b for all
languages. They need separate tickets and separate owners.

---

## Spanish vs German vs English — full comparison

| Bug | English | **Spanish** | German | Spanish verdict |
|---|---|---|---|---|
| **OV#8** glance labels @1440 | fits exactly | **fits exactly** | 3/4 overflow | ✅ **not affected** — German-only |
| **OV#8** glance labels @1024 | +5/+18/+18/+4 | **+27/+18/+21/+27** | +73/+77/+96 | ❌ affected (all langs are) |
| **OV#9** stat-card headers @1024 | (not isolated) | **+51px** ("Incentivación Ver más"), +32px ("Usuarios activos") | +69 / +63 / +35 | ❌ affected — and **Spanish is worse than German on the Incentivización card** (+51 vs +35) |
| **OV#10** "Last 30 Days" subtitle | English (source) | **English** ❌ | English ❌ | ❌ reproduces — confirms wire-up bug is **language-independent**, not German-specific |
| **OV#11** tier row @1024 | — | **+122px** (identical to de) | +122px | ❌ reproduces identically → language-independent |
| **EV#4** event tabs @1024 | 449px in 542px, **93px spare** | **496px in 542px → +2px** (marginal, effectively fits) | **713px in 470px → +291px clipped** | ✅ **essentially not affected** — strongly German-specific |
| **PN#1** `.notif-title` | fits **exactly** 150/150 | **+8px clipped** ("Título de la notificación") | +3px clipped | ❌ **Spanish is WORSE than German** |
| **PN#1** `.two-column-layout` @1024 | +381px | **+516px** | +415px | ❌ **Spanish worst of the three** |
| **SET#3** `.banner-actions` | +67px | **+60px** ("Cambiar banner Quitar") | +73px | ❌ affected, mildest of the three |
| **SET#3** patterns @1024 | 5 | **5** | 6 | ❌ affected — Spanish ≈ English, German worst |
| **MGC#3** card action row @1024 | +30px, 65/103 cards | **+45px, 65/103 cards** ("Ver"/"Gestionar") | +62px, 97 cards | ❌ affected, between en and de |
| **MGC#4 / EV#3** broken images | 5 / — | **5 / 12** | 5 / 12 | ❌ identical counts → language-independent, as expected |

---

## Spanish-specific findings

### 1. PN#1 is worse in Spanish than in German — reclassify as "all non-English"
`.notif-title` is a 150px box that fits "Notification Title" at **exactly** 150px. Spanish "Título de la
notificación" overflows **+8px**; German "Benachrichtigungstitel" only **+3px**. So this zero-headroom
container is not a German problem — it is an **any-language-but-English** problem, and Spanish happens to be
the worst case measured. Same for `.two-column-layout` (es +516 > de +415 > en +381).

### 2. OV#9 has a Spanish-worse case
On the Incentivization stat card, Spanish "Incentivación Ver más→" overflows **+51px** vs German "Anreize
Mehr anzeigen→" at **+35px** — because *Incentivación* is longer than *Anreize*. A German-only test would
have under-reported this card. Good illustration of why single-language layout testing is unsafe even when
that language is "the longest one".

### 3. Spanish is NOT uniformly shorter than German
The common assumption "German is longest, so testing German covers everything" is **false here**. Spanish
was worse on 3 measurements (PN#1 title, PN#1 two-column, OV#9 Incentivización card) and equal on the
language-independent ones. German is worse on compound-noun labels (`Veranstaltungen`,
`Benachrichtigungen`, `Durchschnittlicher`), Spanish on multi-word phrases with prepositions
("Título de la notificación", "Minutos de atención plena").

### 4. Confirmed language-independent (identical in all three languages)
OV#10 ("Last 30 Days" wire-up), OV#11 (tier row +122px), MGC#4/EV#3 (broken images: 5 and 12).
**OV#10 matters:** I originally logged it from the German pass. It reproduces in Spanish, so it is not a
German bug — it is a wire-up bug affecting every language, and its severity is therefore higher than a
single-language finding.

---

## Per-module Spanish results @1024

| Module | Patterns | Broken imgs | Notes |
|---|---|---|---|
| Overview | 20 | 0 | vs 24 in German. OV#8b/#9/#10/#11 all reproduce |
| Manage Challenges | 65 cards | 5 | +45px per card (de +62, en +30) |
| Configuration → Settings | 5 | 0 | ≈ English (de 6) |
| Community → Events | 0 (+2px tabs) | 12 | **EV#4 does not meaningfully reproduce** |
| Comms → Publish Notifications | — | 0 | title +8px (**worse than de**), two-col +516px (**worst**) |

---

## What was NOT done for Spanish
This run deliberately targeted the **already-known German findings** to get the cross-language comparison,
rather than re-sweeping all 19 modules blind. Still open for Spanish:
- [ ] Full 19-module sweep (only 5 modules measured: Overview, Manage Challenges, Settings, Events,
      Publish Notifications). Not yet measured in Spanish: Create Challenge + builder, Past Challenges, all
      6 Reports, Add Employees, Preview Emails, Content Library, Create Content, Create Event, Create
      Announcement, Send Custom Email, Email Designer, Wellness Score, Wellness Leagues, Upload Points.
- [ ] **Translation/string coverage for Spanish** — this run measured *layout only*. Spanish string
      rendering across all 19 modules has still never been verified beyond the original 3-module spot-check
      (gap **G14**). The strings I did observe rendered correctly in Spanish.
- [ ] **Dynamic flows in Spanish** (validation, toasts, live submits) — still German-only (gap **G15**).
- [ ] Widths 1366 / 768 / 375.
- [ ] Everything in `REMAINING_WORK.md` Tier 1–4 applies to Spanish as well (G5 comma-decimal input is
      arguably *more* relevant for Spanish, which also uses a comma decimal separator).
