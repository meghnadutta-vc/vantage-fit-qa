# 08 — ENHANCEMENTS, SUGGESTIONS & JUDGMENT CALLS

P4 items, parity gaps, polish, and calls that need an opinion rather than a fix.

**Deliberate classification:** per the engagement's judgment rules, **not everything missing is a bug.** A
parity or polish gap is an **Enhancement**, not a defect. Items here are separated from files 02–07 so the
real defect list isn't padded.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01
None — nothing here is P1/P2.

---

## Enhancements

### DEL#1 — No delete control exists for challenges · P4 / Enhancement · [FE]
Challenge cards expose only **Ansehen** (view) and **Verwalten** (manage). There is **no delete anywhere**,
which is why challenge **25441** is permanent test debt.
**Why it matters beyond QA:** every challenge created during testing — or by an admin by mistake — is
**unremovable**. Test and junk data accumulate in the tenant forever.
**Not a localization bug.** Raised because it blocks cleanup (see G24 in `10-BLOCKED-NEEDS-DECISION.md`).

> **TERM#2** (casing inconsistency: Polish `Tydzień`/`tydzień`, Russian `Неделя`/`неделя`) is a genuine
> linguistic defect rather than a suggestion, so its canonical entry is `05-LINGUISTIC-QUALITY.md`. Listed
> there, not duplicated here.

### Suggestion — a "nothing to export" message
Clicking **Exportieren → CSV** on a report with no rows fires **no network request and downloads nothing, with
no message**. Defensible behaviour, but a one-line "nothing to export" would be better than silence —
especially given the wider silent-failure pattern in `06-FUNCTIONAL.md`.

### Suggestion — the Discard button gives no confirmation
The Settings **Verwerfen** button discards edits **immediately, with no confirmation**, while *navigating away*
correctly raises a confirm dialog. Inconsistent: the more destructive path (explicit discard) has less
protection than the accidental one. **Judgment call** — arguably fine, but worth a deliberate decision.

---

## Judgment calls — my opinion, for the record

Per the engagement rules, copy/UX calls get an opinion rather than being dumped as bugs.

### SET#1 — Language switcher lists options in English regardless of UI language · P4 · [FE-BE TBD]
The selector always shows `English, Arabic, German, Spanish…` even in a German session.
**My call: CORRECT AS-IS, not a defect.** Language pickers conventionally show each language in **its own**
name (*Deutsch, Español, 中文*) or in a stable reference language. Localizing the list into the *current*
language makes it harder for a user who picked the wrong language to find their way back.
**Recommendation:** switch to endonyms (`Deutsch`, `Español`, `العربية`) rather than translating the list.

### UP#8 — Sample CSV template has English headers and no UTF-8 BOM · P4 · [FE-BE TBD]
The downloadable template headers are English in a German session (`Receiver Employee Name`, `Point`,
`Award Name`), and the file is plain ASCII with **no BOM**.
**My call: NEEDS A PRODUCT DECISION, do not "just fix".** The parser **matches on those English headers**, so
localizing them would **break upload** unless both sides change together. Two coherent options:
1. Keep English headers (safe, machine-contract) and add a localized *instruction* above the download link
2. Localize headers **and** make the parser accept both — more work, better UX

**The BOM is separate and should be fixed regardless:** without it, umlauts and accents mojibake when the file
is opened in Excel. That is a real defect independent of the header question.

### Dutch loanwords — NOT a defect
Dutch keeps `Challenge`, `Team`, `Week` as English/borrowed forms. **Dutch borrows heavily and `week` is a real
Dutch word** — this is idiomatic. **Logged as a glossary/brand policy item, not a bug**, but it should be part
of the glossary decision so it's a choice rather than an accident.

### Arabic numeral system — product decision, not a bug per se
Which system should Arabic use: Arabic-Indic (`٧٣`) or Western (`73`)? **Western digits are common and often
preferred in Arabic business UIs.** The *defect* (AR#3) is **mixing both in one string** — the *choice* is
product's. Decide, then apply to one code path.

### Register policy — the biggest open product decision
Today: **es/it informal · de/fr/ru/hi/zh/id/or formal · nl/ko mixed · ar masculine-only.** No central decision
was ever made. **Recommendation: one register per market, documented in a style guide, then applied.** Detail
in `05-LINGUISTIC-QUALITY.md`.

---

## Process recommendations (for the team, not bugs)

1. **Use Hungarian and Russian as the layout-stress reference languages — not German.** German ranks **4th**
   on text expansion (`hu +119 › ru +68 › pl +65 › de +62`). "Test German because it's longest" proved unsafe
   **three separate times** in this engagement.
2. **Use Chinese as a diagnostic.** CJK compresses, so **breaks that survive in Chinese are exactly the ones
   NOT caused by translation length** — i.e. untranslated English or structural/responsive. A cheap way to
   split those two classes.
3. **Always measure English as a control.** Without it, OV#8a (localization) and OV#8b (responsive) stay
   conflated as one bug.
4. **Verify by direct URL, never after an in-place language switch** (OV#7, ES#1).
5. **Widen fixed-width boxes before shipping wire-up fixes** — PN#2 and WS#2/WL#2 clip *only where the
   translation works*, so translating more will break more layout.

---

# ═══ BACKEND / SOURCE NEEDS TRIAGE ═══

| Item | Note |
|---|---|
| **UP#8** | Header localization is a **shared** FE/BE contract change — the parser and the template must change together. The **BOM fix is frontend/file-generation** and can ship independently. |
| **SET#1** | `[FE-BE TBD]` — if the language list comes from a backend config, the endonym change belongs there. |
| **CL#4** — "Ask Vantage Fit" widget English globally | `[FE-BE TBD]` — a separate embedded product. Likely **out of scope** for this engagement; confirm ownership before logging against Fit. |
