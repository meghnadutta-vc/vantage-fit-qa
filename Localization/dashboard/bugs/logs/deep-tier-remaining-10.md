# Deep-Tier Upgrade — the 10 remaining languages (Run 19, 2026-07-29)

`ru · hu · ko · vi · nl · it · id · or · hi · fr-CA` lifted from Tier 3 (4–6 modules, 1440 only) to
CRUD + expanded modules + **all 4 widths**.

## F4 — CRUD + toast localization: PASSES in all 10

Changed max-team-size 500 → 250, saved, captured the toast, reverted to 500. **Every one localized, every
one reverted.** The `button.save-btn` / `button.discard-btn` class selectors are language-independent, which
made this efficient.

| Language | Save button | Success toast |
|---|---|---|
| Russian | `Сохранить настройки` | `Настройки успешно сохранены.` |
| Hungarian | `Beállítások mentése` | `A beállítások sikeresen mentve.` |
| Korean | `설정 저장` | `설정이 성공적으로 저장되었습니다.` |
| Vietnamese | `Lưu cài đặt` | `Đã lưu cài đặt thành công.` |
| Dutch | `Instellingen opslaan` | `Instellingen succesvol opgeslagen.` |
| Italian | `Salva impostazioni` | `Impostazioni salvate correttamente.` |
| Indonesian | `Simpan pengaturan` | `Pengaturan berhasil disimpan.` |
| Odia | `ସେଟିଂସ୍ ସେଭ୍ କରନ୍ତୁ` | `ସେଟିଂସ୍ ସଫଳତାର ସହ ସେଭ୍ ହେଲା।` |
| Hindi | `सेटिंग्स सहेजें` | `सेटिंग्स सफलतापूर्वक सहेजी गईं।` |
| fr-CA | `Enregistrer les paramètres` | `Paramètres enregistrés avec succès.` |

**fr-CA checked against FRCA#1 and cleared:** its toast is identical to fr, but the dictionary shows
`settings.saved`, `settings.save`, `settings.discard` and `common.discard` are **identical in fr and fr-CA by
design**. So this is *not* a fallback instance. FRCA#1 still rests solely on `overview.scoreBreakdown`.

## Module coverage — the leak set is now proven language-independent at module granularity

Russian (19 modules) and Hungarian (17) produced **identical per-module leak counts**:
`overview 8–9 · create-challenge 0 · past-challenges 0 · events 0 · create-event 0 · announcement 1 ·
send-custom-email 0 · upload-points 1 · add-employees 2 · wellness-score 3 · leagues 3 · redemption 2`

The other eight matched on every module tested. Combined with the 18-language spot-checks, **the leak
inventory (RPT#1, CL#1, CC#1, ANN, `Reward`, `Employee ID`, OV#12) is fully language-independent** — it is a
wire-up problem, not a translation problem, and fixing it once fixes all 18 languages.
Also identical in all 10: Upload Points `58px SPILL @1086`.

## NEW worst-cases at 1920 — the fixed-width group re-ranked

The four width-independent components now have their true worst offenders identified, and **none of them is
German**:

| Component | Box | Worst | Full ranking |
|---|---|---|---|
| **Audience operator** (PN#2) | 50px | **Indonesian `termasuk dalam` +55px** | id 55 › hu 29 › pl 14 = ko 14 › fr/fr-CA 6 › or 5 · *de/es fit (untranslated English)* |
| **`.notif-title`** (PN#1) | 150px | **Russian +21px** | ru 21 › es 8 › de 3 |
| **Wellness Leagues chip** | 110/100px | **Hungarian `Alkalmazotti azonosító` +119px** | hu 119 › nl 73 › ru 68 › pl 65 › it 63 › de 62 › es 58 › pt 55 › fr 53 › hi 49 › or 47 › fr-CA 41 › ar 35 › zh 28 › ko 25 |
| **Report column-selector** | 150px | +31 / +48 — **identical in all 18** (renders English) | — |

**Triage consequence:** PN#2 was logged as a French/Polish issue. It is **worst in Indonesian at +55px** —
more than double the box. And PN#1's worst is Russian, not Spanish. Any fix sizing these boxes must be
validated against **Indonesian, Hungarian and Russian**, not German or French.

## Layout across all 4 widths (Overview / Publish Notifications / Wellness Leagues)

| Lang | 1024 | 1366 | 1440 | 1920 |
|---|---|---|---|---|
| ru | 9 / 4 / 2 | — | 0 / 3 / 2 | 0 / 2 / 2 |
| hu | 9 / 3 / 2 | — | 0 / 3 / 2 | 0 / 1 / 2 |
| ko | 7 / 3 / 2 | — | 0 / 3 / 1 | 0 / 1 / 2 |
| vi | **0** / 2 / 2 | — | 0 / 2 / 2 | 0 / 0 / 2 |
| nl | 5 / 2 / 0 | — | 0 / 2 / 2 | 0 / 0 / 2 |
| it | 8 / 2 / 2 | — | 0 / 2 / 2 | 0 / 0 / 2 |
| id | 8 / 3 / 2 | — | 0 / 3 / 2 | 0 / 1 / 2 |
| or | 7 / 3 / 2 | — | 0 / 3 / 1 | 0 / 1 / 2 |
| hi | 7 / 2 / 2 | — | 0 / 2 / 2 | 0 / 0 / 2 |
| fr-CA | 8 / 3 / 2 | — | 0 / 3 / 2 | 0 / 1 / 2 |

**Overview is clean at ≥1440 in every language** and breaks only at 1024 — confirming the OV#8b
responsive-not-localization split for all 18.
**⚠️ Vietnamese Overview measured 0 breaks at 1024** while every other language showed 5–9. That is an
outlier worth a re-measure — possibly a partial render — and is **not** claimed as a Vietnamese pass.

## OV#7 reproduced a second time (method note)
The Indonesian Wellness Leagues chip rendered **`Minden korcsopor`** — **Hungarian** — in an `id` session
after an in-place language switch. So the `id` chip figure in that pass belongs to Hungarian's string and is
excluded. This is the **second independent reproduction** of OV#7 (the first was Italian-in-Indonesian), both
surfaced by in-place switching. It re-confirms the standing rule: **verify on a fresh load.**

## What still separates these 10 from full German depth
- [ ] **1366** not measured for these 10 (1024 / 1440 / 1920 are done)
- [ ] F1/F2 filter interaction per language (done for es and ar; the dropdown component is shared)
- [ ] F5 dialogs · F7 wizard · F8 logout persistence — **not done in ANY language**
- [ ] U9 register/terminology — done for de (TERM#1/REG#1) and ar (AR#5) only. Korean honorifics,
      Vietnamese formality, Hungarian formal *Ön*, Dutch *u/jij* unexamined.
- [ ] Full 19-module enriched checklist for ko/vi/nl/it/id/or/hi/fr-CA (10–11 modules each; ru 19, hu 17)
