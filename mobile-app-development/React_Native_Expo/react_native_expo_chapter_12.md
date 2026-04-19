# Chapter 12: Testing, Debugging & Store Release

## Learning objectives

- Debug with **Flipper** / React Native dev menu, **logbox**, and **Chrome** (where supported).
- Write **Jest** unit tests for pure logic and **React Native Testing Library** for components.
- Profile performance: **Hermes** sampling, `why-did-you-render` (dev-only).
- Complete **App Store** and **Play Store** checklists: icons, privacy policy, screenshots, review notes.

---

## 1. Debugging toolkit

- **Shake device** or `d` in terminal → dev menu (reload, debug, perf monitor).
- **LogBox** — Red/yellow screens for errors and warnings; fix warnings before release.
- **Native logs** — Xcode / Android Studio logcat for crashes below JS.

---

## 2. Testing strategy

- **Unit tests** — Reducers, validators, API mappers.
- **Component tests** — Render with RNTL; assert accessibility roles and text.
- **E2E** — Detox / Maestro for critical flows (optional capstone).

---

## 3. Release checklist

- [ ] Production bundle (`__DEV__` false) with no debug endpoints
- [ ] Crash reporting (e.g. Sentry) configured
- [ ] Privacy policy URL and in-app data controls if required
- [ ] Store listing assets (6.7" / 5.5" screenshots, feature graphic)
- [ ] Version code / build number bumped per store rules

---

## Summary

- **Ship quality** = debugging discipline + automated tests + store hygiene.
- **Hermes** is standard; learn to read perf traces for janky screens.
- Treat **accessibility** and **privacy** as release blockers, not polish.

---

## Practice

1. Add Jest + RNTL and test a form validation function + one screen snapshot.
2. Fix a deliberate performance bug in a `FlatList` using profiler clues.
3. Draft App Store “What’s New” and review notes for a 1.0 submission.
