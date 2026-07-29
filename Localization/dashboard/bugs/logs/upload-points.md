# Rewards → Upload Points — Localization Bug Log

**Module:** Vantage Fit → Rewards → Upload Points (`/fit/reward-hub/upload-points`)
**Server/tenant:** India `dashboard-v2.vantagecircle.co.in` (company 355, UAT) · German vs English · 2026-07-21

> **CLEAN module — 0 localization defects.** Header, wallet/country selectors, upload-type radios,
> sample-CSV download, drag-drop prompt ("Zum Hochladen klicken oder ziehen und ablegen"), email
> switch, the 6-step guide, and Vorschau/Absenden all localize in German. Only "Reward" (wallet name)
> is English — backend data, not a defect.

## P1 / P2 / P3
_None._

## Cross-module
- `<html lang>` (Overview #4). Upload/validation/toast not exercised (would distribute real points).
