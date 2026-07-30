# 08 — ENHANCEMENTS, SUGGESTIONS & JUDGMENT CALLS

P4 items, parity gaps, polish, and calls that need **an opinion rather than a fix**.

**Deliberate classification:** per this repo's judgment rules, **not everything missing is a bug.** A parity or
polish gap is an **Enhancement**, not a defect. Items live here so the real defect list isn't padded — which
matters, because a 39-bug report that is really 37 bugs plus 2 opinions is less trustworthy than one that says
so.

**Nothing here is P1, P2 or P3.**

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01
**None** — nothing here is P1/P2.

---

## Judgment calls — our opinion is stated, product decides

### B9 — `Wellness Score` stays English in every language · P4 · **our verdict: probably CORRECT as-is**

`Wellness Score` renders in English in de/es/fr/pt and in the French session captured 2026-07-30.

**Why we are not filing this as a defect:** it is plausibly a **brand / product term**, in the same family as
`Vantage Fit` and `Vantage Points`. Brand terms conventionally stay as authored across locales, and translating
them can actively harm recognition.

**What would settle it — one product answer:**

| If product says | Then |
|---|---|
| "brand term, keep English" | → moves to `09-NOT-A-BUG.md`, closed |
| "it should translate" | → becomes a genuine untranslated-string bug, and joins the B39 externalisation scope |

**Do not fix this on QA's initiative.** It is a naming decision, not a defect.

**One complication worth flagging:** whichever way product rules, the same term appears on the **admin
dashboard** too. **Decide it once for both surfaces.**

---

### B34 — The language dropdown lists all option names in English · P4 · **our verdict: DEFENSIBLE, probably correct**

The language picker shows every option in English regardless of the current UI language.

**Why this is likely fine:** there are two established, legitimate conventions for language pickers, and this is
close to one of them:

1. **Endonyms** — each language in its own name (`Deutsch`, `Español`, `Français`). Most common in modern
   products, and arguably the best UX because a user who has accidentally selected a language they can't read
   can still find their way back.
2. **A stable reference language** — every option in one fixed language, so the list never shifts under the
   user. This is what is implemented.

**The failure mode this avoids is real:** if the picker were fully localized, a user who mis-selects Korean
would then see the entire list in Korean and could be genuinely stuck.

**Our recommendation, offered rather than filed:** move to **endonyms** — it is the stronger convention and
solves the same problem more elegantly. But the current behaviour is **not a bug**, and
**it is independent of B39** — worth noting because it would be easy to assume it is another symptom.

---

## Parity observations — not defects

### Fit web has no search surface at all

Confirmed on `/ng/fit/programs`: **zero `<input>` elements**. The admin dashboard has search on its content
library; the employee-facing library does not.

**Framed as an enhancement, not a gap:** with a library of this size, search would be a genuine usability
improvement — and note that the dashboard's search **fails accented input**, so if search is ever added here,
**do not copy that implementation**. Build it with accent folding (`normalize('NFD')` plus stripping combining
marks) from the start.

*(This is also why the accented-input test gap is marked N/A rather than open — see `06-FUNCTIONAL.md`.)*

---

## Content hygiene — worth passing on, not a Fit defect

The employee-facing Programs library displays **obvious test content to real employees**:

| Item | Origin |
|---|---|
| `Managing Workplace Stress: A Practical Guide` | **created during admin-dashboard QA** — recorded there as un-deletable test debt |
| `CREATED FROM SITE PART 3` · `CREATED FROM SITE PART 5` | pre-existing, not from this engagement |
| `This is also a test` · `Youtube link` · `Running Test` | pre-existing |

**Two things worth saying plainly:**

1. **Dashboard test data is visible on the employee surface.** The dashboard engagement disclosed this item as
   cleanup debt; this confirms it is **not contained to the admin view**. Employees can see it.
2. **There is no delete control**, which is why it accumulates. The dashboard logged that as its own
   enhancement (DEL#1). **Same root cause, same recommendation:** give admins a delete path, or the library
   fills with junk permanently.

**Not a localization bug** in either case — raised because it affects real users and blocks cleanup.

---

# ═══ BACKEND ═══

Two backend findings are **enhancement-grade rather than defects**, and are recorded in `11-BACKEND.md` with
that classification:

| Finding | Why it is an enhancement |
|---|---|
| **BE-17** — duplicate adherence activities in the data | Data hygiene. Confusing, not broken |
| **BE-18** — typos in stored content-category names | Content quality. **Also reclassified during review as backend-but-not-localization** — it is a proofreading issue, not a translation one, and filing it as a localization defect would misdirect it |

**BE-18's reclassification is worth noting as a method point:** it was initially logged among the backend
*localization* findings and was moved out on review. Two other findings (BE-14, BE-17) were re-examined at the
same time. **Getting the category right matters more than the count** — a localization ticket that turns out to
be a typo erodes trust in the rest of the report.
</content>
