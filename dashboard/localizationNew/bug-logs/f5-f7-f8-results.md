# F5 · F7 · F8 — Results (Run 21, 2026-07-29)

The three checks that had never been run in any language. German session, 1440.

---

## F5 — Dialogs: **PASSES** ✅ (plus one a11y defect)

The route-guard dialog **does** exist and is **fully localized**. My earlier attempt missed it because it
uses custom `.dialog-*` classes, not CDK overlay panes.

**Trigger:** dirty the Settings form → navigate away **in-app**. (The `Verwerfen` button itself discards
**immediately with no confirmation** — worth knowing, but arguably acceptable UX.)

| Element | Rendered (de) |
|---|---|
| `.dialog-title` | **Änderungen verwerfen?** |
| `.dialog-text` | **Sie haben nicht gespeicherte Änderungen, die verloren gehen, wenn Sie diese Seite verlassen.** |
| `.dialog-actions` | **Abbrechen** / **Verwerfen** |

**Sub-behaviour correct:** navigation was **blocked** (URL unchanged), and **Cancel** kept the page open with
the edit intact (`300`). Cleaned up afterwards — field back to `500`, save bar gone.

**Dictionary audit:** 35 dialog-related keys, **all translated in de and es** — `common.areYouSure`,
`common.confirm/cancel/delete/discard`, `settings.dialog.*`, `announcementPage.deleteHeading/deleteText`,
`events.details.deleteFailed`. **No missing-translation problem in the dialog layer.**

### A11Y#3 — [Accessibility — P3] NEW: the modal has no dialog semantics
| Attribute | Value |
|---|---|
| `role` | **null** |
| `aria-modal` | **null** |
| `aria-labelledby` | **null** |
| `tabindex` | **null** |
| focus after open | **`BODY`** (no focus move into the dialog) |
| `[role=dialog]` anywhere in document | **0** |
| `[aria-modal]` anywhere in document | **0** |

A screen reader will not announce this as a dialog, and keyboard focus is never moved into it or trapped.
Joins **A11Y#1** (no `alt`) and **A11Y#2** (unlabelled icon buttons) — the a11y story is consistent.

### REG#1 reinforced again
Every dialog string is formal ***Sie*** (`Sind Sie sicher?`, `Sie haben nicht gespeicherte Änderungen…`,
`Ziehen Sie zunächst…`) against the product's informal *du* voice. REG#1 now spans headings, dialogs **and**
instructional text — systematic, not incidental.

---

## F7 — Wizard: steps 1→4 verified; **step 5 still not reached**, and now we know why

Walked **Info → Dauer → Zielgruppe → Konfiguration** with validation gating confirmed at every step
(`Weiter` `aria-disabled` flips `true`→`false` only when the step is valid). All steps stayed correctly
German.

**Root cause of the step-5 blocker, finally identified:** step 4 requires **drag-and-drop** —
`Ziehen Sie zunächst Karten aus der Aktivitätsaufgabenliste` ("first drag cards from the activity task
list"). **This is why every earlier click-based attempt failed.** A `dragTo` attempt did not land the card in
the week container, so `Weiter` stayed `aria-disabled="true"` and step 5 (Review) remains unreached.
**Not a product defect** — an automation limitation with this DnD implementation. Reaching step 5 likely
needs manual drag or low-level pointer-event simulation.

**Bonus — the drag did surface the task target field**, which answered an open G5 question:

### G5 — refined with a second field (still NOT a P1)
Challenge task target (`placeholder="Ziel"`, `min=5000`, `max=40000`):

| Typed | Stored | `valid` |
|---|---|---|
| `12,5` | **`""` — field silently cleared** | true |
| `10000,5` | **`""` — field silently cleared** | true |
| `10000.5` | `10000.5` | false |
| `10000` | `10000` | true |

**Two fields, two different comma-decimal behaviours, both silent:**
- **Settings** (integer, max 500): `12,5` → **`125`** — value 10× wrong, `valid:true`, no error
- **Challenge target**: `12,5` → **empty** — input vanishes, `valid:true`, no error

So a German/French/Spanish admin typing `12,5` either gets a wrong number or loses their input, and **is told
nothing either way**. Confirms G5 as a real **P3** defect class with no P1 found — the target field clears
rather than corrupts, and Settings is bounded by its max clamp.

---

## F8 — Persistence: **THE KEY FINDING — the language preference is client-side only**

### F8#1 — [Functional / Localization — P2] Language preference is not persisted server-side
**Module:** global · **[FE]** · **gap G3**

Removed `localStorage.fit_lang` (simulating cleared client state / a fresh device) and cold-loaded
`/fit/overview`:

| Observation | Result |
|---|---|
| `fit_lang` after reload | **`"en"` — the app wrote English back** |
| Rendered language | **fully English** (`All Countries`, `Last 30 Days`, `Enrolled Users`, `View more`) |
| Language selector | reset to **English** |
| German strings on page | **0** |

**The admin's language choice lives only in browser `localStorage`.** There is no account-level or
server-side persistence, so the selection is **lost on any new browser, new device, incognito window, or
cleared site data** — the admin silently reverts to English and must re-select every time.

**Why P2:** this is the dashboard analogue of **B11**, logged **P2 on the employee web**. It is a broken
user flow for every non-English admin, not a cosmetic issue.
**Confirmed reversible:** restored `fit_lang=de` and verified German renders again (selector shows German).

### Scope limits — stated honestly
- **The literal logout→login leg was NOT performed.** The dashboard-v2 profile menu exposes only the account
  identity — **there is no logout control there** — and the perks parent app showed no logout among 41
  visible controls without deeper digging. Logging out of the user's parent session is a broader action than
  this test warrants, and the architectural finding above already establishes the important fact (client-only
  persistence). **Whether logout additionally clears `localStorage` remains the one open sub-question.**
- **Accept-Language precedence (G23) is inconclusive here:** this browser reports `en-GB, en-US, en`, so
  "defaults to `en`" cannot be distinguished from "follows `Accept-Language`". Needs a browser configured to a
  non-English locale.

---

## Net status of the three
| Check | Result |
|---|---|
| **F5 dialogs** | ✅ **PASSES** — localized, blocking, Cancel correct. New **A11Y#3** (no dialog semantics). |
| **F7 wizard** | ◐ steps 1–4 pass; **step 5 blocked by drag-and-drop**, cause now identified |
| **F8 persistence** | ❌ **F8#1 [P2]** — client-side only, resets to English on any fresh client |
