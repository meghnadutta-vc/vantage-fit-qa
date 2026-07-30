# 07 — ACCESSIBILITY

**This is the first accessibility data ever collected on this surface**, and unlike the admin dashboard it
includes **measured contrast and real keyboard traversal**, not only attribute auditing.

All items are **language-independent** (same markup in every language) but had never been measured. One owner,
one fix pattern — which is why they are grouped rather than scattered.

**None of these reached P2.** That is a severity judgment against the scale (no crash, no data loss) and
**not** a claim they don't matter: for a screen-reader or keyboard-only user, B36 and B30 together make the
water-logging flow unusable.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01
**None** — no accessibility bug reached P2.

---

## 🔴 B37 — [P3] Seven text elements fail WCAG AA contrast, worst at 1.79:1 · [FE]

**Measured, not estimated.** WCAG AA requires **4.5:1** for normal text and **3:1** for large text.

| Element | Ratio | Required | Severity of the miss |
|---|---|---|---|
| **Deficit *data value*** | **1.79:1** | 4.5:1 | **worst in the app — a number the user is meant to read** |
| Active nav pill | 3.45:1 | 4.5:1 | fails |
| 5 further text elements | below 4.5:1 | 4.5:1 | fail |

**Why the worst one matters most:** 1.79:1 is on a **data value**, not decorative chrome. The user is being
asked to read a health number that is very nearly invisible. In a health product that is the wrong place to
have the worst contrast in the build.

---

## 🔴 B36 — [P3] Custom controls have no accessible semantics and no keyboard support · [FE]

The water-amount slider is a **plain `<div>`** with:

| Expected | Present |
|---|---|
| `role="slider"` | **absent** |
| `aria-valuenow` / `aria-valuemin` / `aria-valuemax` | **absent** |
| keyboard operability (arrow keys) | **absent** |
| focusability | **absent** |

**So the control is unusable by keyboard and invisible to a screen reader** — there is no way to log water
without a pointer.

### The pattern is now confirmed on FOUR surfaces

| Surface | Finding |
|---|---|
| Log Water — amount slider | custom `div`, no ARIA, no keyboard |
| Log Water — 2 steppers | same shape *(the `+` stepper does correctly expose `disabled: true` at the 8h cap — partial credit)* |
| **Hiking activity form** | **`inputs: []` — zero native inputs.** Duration stepper, distance field and calories field are **all** custom DIVs |

**This is a design-system-level pattern, not four separate bugs.** Fixing the shared control primitives fixes
all of them — which makes it far better value than four individual patches.

---

## 🔴 B30 — [P3] Modals have no dialog semantics and do not move focus · [FE]

| Attribute | Value |
|---|---|
| `role` | **null** |
| `aria-modal` | **null** |
| accessible name (`aria-label` / `aria-labelledby`) | **null** |
| `document.activeElement` after open | **`BODY`** — focus is never moved in |

**Consequences:** a screen reader does not announce that a dialog opened, and a keyboard user can tab into the
page **behind** a modal that is visually blocking it.

### Confirmed on THREE modals

Log Water · **Log Activity picker** · **Hiking activity form** — all identical.

**That three-for-three result is the useful part:** it means this is almost certainly the **shared modal
component's default**, not three omissions. The admin dashboard found exactly the same thing and confirmed
**zero elements anywhere in the document** carry `role=dialog` or `aria-modal`. **Fix the component once and
every dialog in both products is fixed.**

---

## B35 — [P2] Arabic bidi reversal — also an accessibility defect · [FE]

Canonical entry: `03-UI-LAYOUT.md`. Listed here because reversed painted order affects anyone reading visually,
and because a screen reader following DOM order will read something **different from what is on screen** — the
two disagree, which is its own class of confusion.

---

## ✅ What was measured and PASSED

| Check | Result |
|---|---|
| **`<html lang>` reflects the selected language** | **PASS** — measured `lang="fr"` in a French session. **Notably better than the admin dashboard**, where `<html lang>` is stuck at `"en"` in all 18 languages |
| **`dir` attribute in Arabic** | **PASS** — set correctly; layout mirrors. Again the **opposite** of the dashboard, where RTL is absent entirely |
| **Keyboard focus indicator** | **PASS** — a real Tab press shows `:focus-visible` at `2px solid rgb(101,74,183)` |

### ⚠️ The focus indicator was nearly filed as a bug — how the false negative happened

A programmatic `.focus()` suggested **4 of 10 controls had no visible focus indicator.** That was on its way to
being logged.

**Driving a real Tab key showed the indicator present on all of them.** The cause: **`:focus-visible` does not
activate for programmatic focus** — correct browser behaviour, not an app defect.

**Rule: any focus audit must drive real keyboard input.** This is recorded prominently because the same trap
will catch the next person.

---

## Not tested — listed so nobody assumes these passed

| Dimension | Status |
|---|---|
| Screen-reader announcement (actual, with a real reader) | **not done** — `<html lang>` passing is a good sign but is not the same as verifying speech output |
| Focus **order** beyond the modals | not done |
| Whether toasts are announced (`aria-live`) | **not done** — toasts were captured programmatically, never verified as announced |
| Full keyboard-only traversal of complete flows | not done |
| Touch-target sizing (44×44px) | **not done on web** (was checked on Android) |
| Contrast in **other languages / dark mode** | only the default theme measured |

**Honest framing:** contrast and keyboard focus were genuinely **measured** here, which puts this ahead of the
dashboard's attribute-only audit. But a full accessibility audit needs a real screen reader and complete
keyboard traversal. **This is a good first pass, not an audit.**

---

# ═══ BACKEND ═══

**No backend accessibility defects** — accessibility is entirely a frontend/markup concern.

One boundary note: **B23** (thumbnails rendering as black boxes, cause = **BE-14**, malformed paths in stored
data) **compounds** the accessibility picture, because those images also have **no `alt` text**. The user gets
**neither the image nor a text fallback**.

**Useful sequencing:** adding `alt` is a **frontend** fix and is **not blocked** on the backend path cleanup —
so it can ship first and mitigates B23 in the meantime.
</content>
