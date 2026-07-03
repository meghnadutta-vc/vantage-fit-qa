# Test Scope & Coverage Tracker — Vantage Fit (Android UI/UX)

Master checklist of the whole app's UI/UX testing. Updated as runs complete.

- **Build under test:** VFit PROD new design fixes 16_jun.apk · emulator-5554 (Android 16) · driver: adb + uiautomator
- **Last updated:** 2026-06-26
- **Bugs to date:** 51 logged (1 P1 crash, see `bug-logs/bug-log.md`)

**Legend:** ✅ Done · 🟡 Partial · ⬜ Not started · ⛔ Blocked (environment) · 🚫 Excluded (destructive / by request)

---

## A. Authentication & Onboarding — ⬜ NOT STARTED
| Area | Status | Notes |
|---|---|---|
| Splash / launch | ⬜ | Not formally tested |
| Login (email/password) | ⬜ | All runs assume already logged in (Demo account) |
| Signup / registration | ⬜ | |
| Forgot / reset password | ⬜ | |
| SSO / OAuth (Google etc.) | ⬜ | |
| Onboarding walkthrough | ⬜ | |
| Logout | 🚫 | Excluded — destructive on shared account |
| Delete Account | 🚫 | Excluded — irreversible |

## B. Home / Dashboard — 🟡 PARTIAL
| Area | Status | Notes |
|---|---|---|
| Summary cards (Steps, Active Minutes) | 🟡 | Seen/read; not deep-tested |
| Trends (Avg Steps, Active Min, Mindful Min, Avg Sleep) | 🟡 | Rendered; deeper interactions not exercised |
| League banner (Bronze/Silver…) | ⬜ | Not opened/tested |
| Pull-to-refresh / scroll states | ⬜ | |

## C. Bottom Navigation Tabs
| Tab | Status | Notes |
|---|---|---|
| **Home** (dashboard) | 🟡 | See section B + Summary modules (E) |
| **Challenges** | ✅ | *(Run 5)* Ongoing/Upcoming/Past, detail, tasks, leaderboard, more-info. Active-challenge flows blocked (no enrolled challenges) |
| **Programs** | ⬜ | **NOT TESTED** — major section, untouched |
| **Community** | ⬜ | **NOT TESTED** — major section, untouched |

## D. FAB Quick Actions — ✅ DONE *(Run 1)*
| Area | Status | Notes |
|---|---|---|
| FAB sheet open/close | ✅ | |
| Log Activity ("All Activities") | ✅ | Bugs #1–#15 area |
| Measure Heart Rate | 🟡 | Disclaimer back-trap (Bug #8); full measurement not run |
| Log Today's Meal (meal-type tabs) | ✅ | |

## E. Summary Detail Modules — ✅ DONE *(Run 2)*
| Module | Status | Notes |
|---|---|---|
| Calendar | ✅ | Module 2 |
| Calorie / Meal log (Nutrition) | ✅ | Module 3 |
| Water log | ✅ | Module 4 (success-dialog back-trap #24) |
| Sleep | ✅ | Module 5 |
| Health Profile | ✅ | Module 6 (Current City data #29) |
| Device Connection | ⛔ | Blocked — needs Google account on emulator (#31) |

## F. Home Header (right-side icons) — ✅ DONE *(Run 3)*
| Icon | Status | Notes |
|---|---|---|
| Wallet | ✅ | → Points Statement empty state (#36) |
| Notifications | 🟡 | List + badge-clear done; individual notification tap not done |
| Profile / League avatar | 🔴 | **P1 CRASH (#34)** — closes the app |

## G. App Menu / Navigation Drawer — ✅ DONE *(Run 4)*
| Area | Status | Notes |
|---|---|---|
| Drawer container (open/close/swipe/items) | ✅ | Bottom sheet (#42, #43) |
| Profile (view) | ✅ | |
| Profile (edit) | ✅ | **Edits don't save — P2 #38** |
| App Preferences → Unit Settings | ✅ | Persists correctly |
| App Preferences → Reminder Settings | ✅ | Persists correctly |
| App Preferences → Leaderboard Settings | ✅ | Opt in/out works |
| App Preferences → Sync Activities | ✅ | No feedback (#44) |
| App Preferences → Change Device | ⛔ | Routes to device flow (Google-blocked) |
| Quick Links → My Workouts | ✅ | → Health Connect onboarding |
| Quick Links → My Badges | ✅ | |
| Wallet → Redeem Points | ✅ | Catalog + detail (redeem not executed) |
| Wallet → Points Statement | ✅ | (covered in Run 3) |
| Wallet → My Gift Cards | ✅ | Empty state (#46) |
| More → Terms and conditions | ✅ | WebView |
| More → Privacy Policy | ✅ | Duplicates Terms (#45) |
| More → Rate us | ✅ | → Play Store |
| More → Need Help? | ✅ | → Freshchat chat (message not sent) |

## H. Cross-Cutting Concerns — ⬜ MOSTLY NOT STARTED
| Concern | Status | Notes |
|---|---|---|
| Accessibility — full TalkBack pass | 🟡 | Only content-desc spot-checks (#33, #39, #40); no end-to-end screen-reader run |
| Leaderboard — actual rankings view | ⬜ | Only the opt-in *setting* tested, not the leaderboard screen |
| Localization / language switching | ⬜ | Planned as a separate area (`localization/` folder) |
| Orientation / rotation | ⬜ | |
| Offline / airplane-mode behaviour | ⬜ | |
| Performance / jank / cold-start | ⬜ | (Play Core instability noted, #35) |
| Push notifications (receipt + deep-link) | ⬜ | |
| Deep links / app links | ⬜ | |
| Search (if present) | ⬜ | Not yet located |
| Logcat crash traces (e.g. for #34, #38) | ⬜ | Offered; not captured |

---

## Snapshot
- **Done:** FAB Quick Actions · Summary detail modules · Home Header · Navigation Drawer (incl. Profile, App Preferences, Quick Links, Wallet, More) · **Challenges**
- **Biggest gaps (not started):** **Programs, Community** bottom-nav tabs · Authentication/onboarding · most cross-cutting concerns (accessibility full pass, localization, offline, rotation, performance, push, deep links)
- **Blocked / data-gated:** Device Connection / Change Device (Google account) · Health Connect import (system setup) · **active-challenge flows** (no enrolled challenges on the Demo account)
- **Excluded by request:** Logout · Delete Account
