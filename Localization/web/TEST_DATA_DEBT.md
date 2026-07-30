# Test-data debt created by employee-web localization QA

**Disclosed for cleanup.** Recorded **before** each write, per the repo rule that irreversible actions are
noted first.

**Account:** the UAT QA account · **Surface:** `app.vantagecircle.co.in/ng/fit/*`

---

## 2026-07-30 — one activity record, created deliberately

**What:** one **Hiking** activity logged from the Quick Add → Log Activity flow.

| Field | Value |
|---|---|
| Activity | Hiking |
| Date | 2026-07-30 (today) |
| Duration | 30 min (form default) |
| Distance | 5.0 km (form default) |
| Calories | 384 (form-estimated) |
| Session language | **French** |

**Why it was created:** gap **W13** — toast localization on a successful write had never been tested, in either
direction. Earlier "no toast" results were captured **without** the required ~2 s wait, so they were
*unconfirmed*, not negative. The only way to close it is to complete one real write.

**Why it is permanent:** there is **no delete control** for logged activities on this surface *(to be
re-verified below — if a delete path does exist, this debt is removable and that is itself a finding)*.

**Blast radius:** self only. Nothing outward-facing. No other user affected. Values are realistic rather than
junk, per the repo's "formal content names" rule — so if this record is ever seen by a human it reads as a
normal entry, not as obvious test litter.

**Authorised by:** the user, explicitly, after being shown the cost and the alternatives.

---

## Deletability check

**Result: NOT DELETABLE — confirmed 2026-07-30.**

| Check | Result |
|---|---|
| Interactive controls inside the activity row | **none** |
| The words *delete / supprimer / remove / retirer* anywhere on the page | **absent** |

**So this record is permanent** and the "no delete control" claim in `bugs/10-BLOCKED-NEEDS-DECISION.md` is
**confirmed rather than assumed.** The debt stands as disclosed above.

**Value returned for it:** gap **W13** is closed (there is no success toast at all — see `bugs/bug-log.md`
addendum 20), the **B31** error-path bug was reframed into the much stronger *"the app never confirms or denies a
write"*, and **B40** was found — a P2 where a French user misreads their own logged distance by 1000x.
