# Translation Suggestions — Vantage Fit Admin (German) — Run 1, 2026-07-10

Style / word-choice / idiom suggestions from the PASS 5 accuracy review. **These are NOT bugs**
(they are not wrong, ungrammatical, or rude). All flagged **"needs native-speaker sign-off"** per
TEST-PLAN. Confirmed grammatical errors and rude/wrong-register tone are logged as Copy **bugs** in
`bug-logs/bug-log.md` instead (e.g. Bug #7 capitalization).

## Overall accuracy assessment
The German translations that ARE present are **high quality**: accurate to context, grammatically
correct, and consistently using the **formal "Sie"** register appropriate for an enterprise admin
product (e.g. "Verwalten Sie Ihre laufenden … Challenges", "Beginnen Sie mit dem Erstellen…",
"Passen Sie Ihre Filter … an"). No rude, overly casual, or machine-stiff phrasing was found. The
dominant problem in this build is **coverage (untranslated strings), not translation quality.**

## Suggestions (judgment calls — optional polish)

| # | Screen | English | Current German | Suggestion | Rationale |
|---|---|---|---|---|---|
| S1 | Nav group header | Engage | "Einbinden" | Consider "Engagement" or "Aktivieren" | "Einbinden" (to embed/involve) is understandable but slightly indirect for the employee-engagement sense; a native speaker may prefer a more idiomatic term. Judgment call. |
| S2 | Active Challenges | "1 participant" | "1 Teilnehmende" | "1 Teilnehmende:r" / "1 teilnehmende Person" | Singular of the substantivized participle reads awkwardly; ICU plural handling for the 0/1/many cases would fix it. (Also logged as doubt N5.) |
| S3 | Loanword consistency | Challenge/Community/Event/Wallet/Score/Bite Size/Social Feed/In-App | kept in English | Keep, but confirm against a glossary | These English loanwords are common and acceptable in German wellness/enterprise copy; keeping them is defensible. Worth locking into the termbase so usage stays consistent across languages. |
| S4 | Badges | NEW / FREE | mixed ("NEU" on cards, "NEW"/"FREE" in nav) | Decide one policy | Not a translation-quality issue per se, but a consistency decision — either localize badges everywhere ("NEU"/"KOSTENLOS") or keep as brand tags everywhere. (Inconsistency itself logged as Bug #8.) |

> Add rows here as more languages are tested. When the team provides a glossary/termbase, validate
> loanword and terminology choices (S3) against it.
