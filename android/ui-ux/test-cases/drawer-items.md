# Test Cases — Drawer ▸ Quick Links / Wallet / More

**Source:** Navigation drawer. Covers QUICK LINKS (My Workouts, My Badges), WALLET (Redeem Points, My Gift Cards), and MORE (Terms, Privacy Policy, Rate us, Need Help?). *(Wallet "Points Statement" covered in Run 3 / home-header.md.)*
**Build:** VFit PROD new design fixes 16_jun.apk · emulator-5554, Android 16 (API 36).

| Test Case ID | Description | Preconditions | Steps | Expected Result | Actual Result | Status | Priority |
|---|---|---|---|---|---|---|---|
| DRWI-TC-001 | My Workouts — open | Drawer open | Tap "My Workouts" | AllWorkoutListActivity opens | Opens ✓ | | P3 |
| DRWI-TC-002 | My Workouts — empty state | No workouts | View screen | Friendly empty state + guidance | "No workouts yet! This section will update automatically…" + illustration ✓ | | P3 |
| DRWI-TC-003 | My Workouts — Import from Health Connect | On My Workouts | Tap "Import Workouts" → "Enable Health Connect" | Routes to Health Connect onboarding/permissions | Routes to HealthConnectPermissionActivity → system Health Connect onboarding ✓ | | P3 |
| DRWI-TC-004 | My Badges — open & render | Drawer open | Tap "My Badges" | BadgeActivity shows earned + locked badges | Opens; Best Walk Badge hero, Beginner/Marathons/Daily Steps categories ✓ | | P3 |
| DRWI-TC-005 | My Badges — earned vs locked states | On My Badges | Inspect badge tiles | Earned = colored, locked = greyed; clear distinction | Clear states (3K/5K/7K earned colored; others greyed) ✓ | | P3 |
| DRWI-TC-006 | My Badges — accessibility labels | TalkBack | Focus badge images | Each badge image announces a label | Badge images carry content-desc (e.g. "10K Badge") ✓ | | P3 |
| DRWI-TC-007 | Redeem Points — catalog | Drawer open | Tap "Redeem Points" | RedeemListActivity lists available gift cards | Lists Mastercard, Sephora, Visa, Southwest, Wayfair, Pottery Barn… all "Available" ✓ | | P3 |
| DRWI-TC-008 | Redeem Points — card detail | On catalog | Tap a gift card (e.g. Sephora) | RedeemDetailsActivity: denomination, quantity, T&C, balance, Redeem | Shows Unit Price $5, Quantity 1, T&C, balance 0, Redeem button ✓ | | P3 |
| DRWI-TC-009 | Redeem with insufficient points (negative) | Balance 0 | On detail → tap Redeem | Blocked with a clear "not enough points" message | **NOT executed** — redemption is irreversible ("cannot be cancelled"); needs a funded account + go-ahead | | P2 |
| DRWI-TC-010 | Redeem — denomination string sanity | On catalog | Read denomination lists | Denominations in sensible order | "…/100/200/250/2/3/4" — trailing 2/3/4 out of order (Bug #47) | | P4 |
| DRWI-TC-011 | My Gift Cards — empty state | No vouchers | Tap "My Gift Cards" | Clear, friendly empty state | MyVouchersActivity: "Sorry!! / No Data Found" + Refresh — weak/dev-ish copy (Bug #46) | | P4 |
| DRWI-TC-012 | Terms and conditions — open | Drawer open | Tap "Terms and conditions" | WebView loads the T&C document | WebViewActivity loads full T&C content ✓ | | P3 |
| DRWI-TC-013 | Privacy Policy — open & correctness | Drawer open | Tap "Privacy Policy" | WebView loads the **Privacy Policy** (distinct from T&C) | WebView loads, but body is essentially identical to T&C (Bug #45) | | P3 |
| DRWI-TC-014 | Rate us — routing | Drawer open | Tap "Rate us" | Opens Play Store listing for the app | Opens Google Play "Vantage Fit (Beta)" listing ✓ | | P3 |
| DRWI-TC-015 | Need Help? — routing | Drawer open | Tap "Need Help?" | Opens support/help (chat or web) | Opens Freshchat support chat (ConversationDetailActivity) ✓ | | P3 |
| DRWI-TC-016 | Need Help? — send message (not executed) | In chat | Type + Send | Message delivered to support | **NOT executed** — would create a real support ticket on the test account | | P3 |
| DRWI-TC-017 | Back navigation from each destination | In any item screen | Press Back | Returns to dashboard/drawer cleanly | Back returns cleanly from all (incl. Play Store, Health Connect) ✓ | | P3 |
