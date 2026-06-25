# Vantage Fit — QA Workspace

QA / manual-testing workspace for the **Vantage Fit** Android app (Vantage Circle).
This folder holds test notes, bug reports, and helper scripts. It is **not** the app source.

## App under test

- **App name:** VFit (Vantage Fit)
- **Package:** `com.bargaintechnologies.vantagefit.v_fit`
- **APKs:** dropped in `~/Downloads/` (e.g. `VFit PROD new design fixes 16_jun.apk`)
- **Target:** Android emulator (`emulator-5554`)

## Environment

- `adb` lives at `~/Library/Android/sdk/platform-tools/adb` (not always on PATH).
- Emulator is managed via Android Studio → Device Manager.

## Common commands

```bash
# adb shortcut
ADB=~/Library/Android/sdk/platform-tools/adb

# list running devices/emulators
$ADB devices

# install / reinstall an APK (keeps app data)
$ADB install -r "~/Downloads/<file>.apk"

# fresh install (uninstall first)
$ADB uninstall com.bargaintechnologies.vantagefit.v_fit
$ADB install "~/Downloads/<file>.apk"

# launch the app
$ADB shell monkey -p com.bargaintechnologies.vantagefit.v_fit -c android.intent.category.LAUNCHER 1

# clear app data (reset to fresh state)
$ADB shell pm clear com.bargaintechnologies.vantagefit.v_fit

# screenshot the emulator
$ADB exec-out screencap -p > screenshot.png

# capture logs while reproducing a bug
$ADB logcat | grep -i vantagefit
```

## Conventions

- File bug reports as `bugs/YYYY-MM-DD-short-title.md` with steps, expected vs actual, and a screenshot.
- Note which APK build was tested (filename / build date) in every report.
