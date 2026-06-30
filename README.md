# ChaiChaiEmbyTV Quest 3 Fix

This repository publishes a Quest 3 compatibility rebuild of `ChaiChaiEmbyTV-0.3.0-alpha6-universal.apk`.

## Download

Use the APK attached to the GitHub Release:

- `ChaiChaiEmbyTV-0.3.0-alpha6-quest3-aligned-debugSigned.apk`

## What Changed

The APK was rebuilt from the original `0.3.0-alpha6` package with a small input compatibility patch in `MainActivity`:

- Converts controller/mouse `ACTION_BUTTON_PRESS` and `ACTION_BUTTON_RELEASE` events into ordinary touch down/up events.
- Adds a fallback click path for common gamepad/controller keys: `BUTTON_A`, `BUTTON_L2`, `BUTTON_R2`, and `BUTTON_SELECT`.

This is intended to make Meta Quest 3 controller trigger clicks work in the app's Compose UI.

## Package Info

- Package: `com.dh.myembyapp`
- Version: `0.3.0-alpha6`
- Version code: `30`
- Min SDK: `23`
- Target SDK: `36`
- APK SHA256: `65b5c181d772e42355a57997bd7326b792a797c930eecd703b1106da7ce6a4a7`

## Install Note

This APK is debug-signed after rebuilding. If the original app is already installed on Quest 3, uninstall the original package first because the signing certificate is different.
