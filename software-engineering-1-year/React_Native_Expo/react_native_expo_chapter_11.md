# Chapter 11: EAS Build, Updates & Environment Config

## Learning objectives

- Configure **`eas.json`** profiles: `development`, `preview`, `production`.
- Run **cloud builds** for iOS and Android; understand credentials and Apple Developer requirements.
- Use **EAS Update** for OTA JS updates within Expo’s guidelines.
- Manage **environment variables** (`EXPO_PUBLIC_*`) and secrets on EAS.

---

## 1. EAS Build overview

**EAS Build** compiles native binaries in the cloud (or locally). You need:

- **Apple Developer** account for App Store iOS builds.
- **Google Play** developer account for Play uploads.

`eas build --platform ios` / `android` produces installable artifacts.

---

## 2. Channels and branches

Link **update branches** to **channels** so staging apps receive staging updates and production only receives production bundles.

---

## 3. OTA limitations

OTA updates can change **JavaScript and assets** managed by Metro. Changes requiring **native code** or new permissions need a **new store build**.

---

## Summary

- **Profiles** separate dev, preview, and prod pipelines.
- **Updates** accelerate iteration but cannot replace all native changes.
- **Secrets** stay off the client; use EAS env for build-time injection.

---

## Practice

1. Create `eas.json` with preview and production profiles.
2. Ship a trivial JS change via EAS Update to a preview channel.
3. Document which changes always require a new binary vs OTA.
