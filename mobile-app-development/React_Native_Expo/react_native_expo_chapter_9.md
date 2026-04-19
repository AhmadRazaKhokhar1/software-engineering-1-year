# Chapter 9: Device APIs — Permissions, Camera & Media

## Learning objectives

- Request runtime permissions on iOS and Android with **`expo-image-picker`**, **`expo-camera`**, or **`expo-media-library`** as appropriate.
- Explain why permissions strings must be user-facing and localized in `app.json`.
- Handle denial: degrade gracefully and link to Settings when needed.
- Use **Expo modules** that match your SDK version; read migration notes each upgrade.

---

## 1. Permission flow

1. Check existing status with `getPermissionsAsync` / `requestPermissionsAsync`.
2. If denied, show in-app rationale before re-requesting.
3. If permanently denied, deep-link to app settings (`Linking.openSettings()`).

---

## 2. Camera vs image picker

- **Image picker** — User chooses from library or captures one shot — simplest for many apps.
- **Camera** — Live preview, barcode scanning, custom capture UX.

Pick the **smallest API** that meets the product requirement (fewer permissions = higher install conversion).

---

## 3. Privacy

Disclose data use in App Store / Play listings. Only request permissions when a feature is used (“just in time”).

---

## Summary

- **Permissions** are part of UX — request in context.
- Match the **module** to the feature; don’t over-collect.
- Handle **denied** states as first-class flows.

---

## Practice

1. Implement “Change avatar” via library with permission handling.
2. Add a barcode scan screen using the appropriate Expo module (per current SDK docs).
3. Write copy for `NSPhotoLibraryUsageDescription` (iOS) explaining why access is needed.
