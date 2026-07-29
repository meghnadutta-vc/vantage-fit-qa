# 07 — ACCESSIBILITY

**Dimension G19 was at zero before this engagement — this is the first accessibility data collected.**

All items are **language-independent** (same DOM in all 18 languages) but were never measured. One owner, one
fix pattern — which is why they are grouped here rather than buried in a P3 catch-all.

---

# ═══ FRONTEND ═══

## ⚠️ Repeats from file 01
None — no accessibility bug reached P2. **All are P3/P4.** That is a severity judgment, not a claim that they
don't matter: for a screen-reader user, A11Y#1 + A11Y#3 together make parts of the product unusable.

---

### OV#4 / AR#4 — `<html lang>` is `"en"` in every language, on every module · P3 · [FE] (CROSS-MODULE)
`document.documentElement.lang` is **always `"en"`** regardless of `fit_lang`. Confirmed on **22 German
modules** and on every module in **all 18 languages**.
**Impact:** screen readers apply **English pronunciation rules** to German, French, Polish, Russian, Hindi,
Odia and Chinese content. Text-to-speech becomes unintelligible.
**Worst in Arabic (AR#4):** a screen reader needs **both** the language *and* the direction, and it has
neither (see AR#1).
**Fix:** set `lang` from `fit_lang` on switch. Single-line change, engagement-wide benefit.

### A11Y#1 — Images have no `alt` text, at scale · P3 · [FE] (CROSS-MODULE)
| Module | Images with no `alt` |
|---|---:|
| Manage Challenges | **103** |
| Past Challenges | **24** |
| Create Challenge | **9** |
| Events | 1 |

The attribute is **absent entirely**, so screen readers announce filenames or nothing.
**Expected:** meaningful `alt` on content images, `alt=""` on decorative ones.
**⚠️ Compounds MGC#4 / EV#3:** the **5 and 12 broken images** also have no `alt` — so the user gets
**neither the image nor a text fallback**. Fixing `alt` mitigates the broken-image bugs even before the CDN
URLs are fixed.

### A11Y#3 — Modal has no dialog semantics or focus management · P3 · [FE]
The Settings route-guard dialog (which is otherwise correctly localized — see `06-FUNCTIONAL.md`):
| Attribute | Value |
|---|---|
| `role` | **null** |
| `aria-modal` | **null** |
| `aria-labelledby` | **null** |
| `tabindex` | **null** |
| Focus after open | **`BODY`** — focus is never moved into the dialog |
| `[role=dialog]` anywhere in document | **0** |
| `[aria-modal]` anywhere in document | **0** |

A screen reader **will not announce it as a dialog**, and keyboard focus is neither moved in nor trapped — so
a keyboard user can tab into the page behind a modal that is visually blocking.
**Note the zero counts:** no element **anywhere** in the document has `role=dialog` or `aria-modal`, which
suggests this is the **dialog component's default**, not a one-off omission. Fixing the component fixes every
dialog.

### A11Y#2 — Icon-only buttons with no accessible name · P3 · [FE] (CROSS-MODULE)
| Module | Unlabelled icon controls |
|---|---:|
| Create Event | **4** |
| Create Announcement | 2 |
| Publish Notifications | 2 |

No text content, no `aria-label`, no `title`. Extends **SET#2** and **CL#5** (previously logged as single
instances) into a cross-module pattern.
**Partial good news:** Content Library row actions **do** carry localized `title` attributes
(`Inhalt ansehen`, `Inhalt bearbeiten`) — so the pattern is inconsistent rather than absent, and there is a
working example in-codebase to copy.

### SET#2 — "Max team size" info icon has no accessible label · P4 · [FE]
Specific instance of A11Y#2.

### CL#5 — Action-column icon buttons have no accessible name · P4 · [FE]
Specific instance of A11Y#2. **Superseded in part** by the Content Library `title` finding above — re-verify
which columns still lack names.

---

## RTL accessibility — blocked

**AR#1** (RTL not implemented, **P2**, file 01) is also an accessibility defect: without `dir="rtl"`, assistive
technology has no way to know the content direction.
**The following cannot be audited until `dir="rtl"` exists** — there is no point measuring mirroring while
direction is globally LTR:
- icon / chevron mirroring
- logical padding and margin (`padding-inline-*`)
- table column order
- slider and progress-bar direction
- focus order in a mirrored layout

**Re-test the whole Arabic layout after RTL ships — expect a fresh crop of bugs at that point.**

---

## Not tested — a11y depth

| Dimension | Status |
|---|---|
| Colour contrast | ✗ never measured |
| Focus order (beyond the dialog) | ✗ |
| Screen-reader announcement language | ✗ (predicted broken by OV#4, not verified with a real SR) |
| `aria-live` on toasts | ✗ — toasts were captured via MutationObserver, not verified as announced |
| Keyboard-only navigation of the full flows | ✗ |
| Touch-target sizing (≥44×44px) | ✗ on dashboard (was checked on Android) |

**Honest framing:** what exists here is **attribute auditing**, not an accessibility audit. A real audit needs
a screen reader, a contrast tool and keyboard-only traversal. Everything above is what could be proven from
the DOM.

---

# ═══ BACKEND ═══

**No backend accessibility defects** — accessibility is entirely a frontend/markup concern.

One boundary note: **A11Y#1** interacts with backend/CDN work — the broken images (MGC#4 / EV#3) are
`[FE-BE TBD]` because the malformed URLs may originate in stored content paths. But the **missing `alt` is
frontend regardless of where the image URL comes from**, so the a11y fix is not blocked on that triage.
