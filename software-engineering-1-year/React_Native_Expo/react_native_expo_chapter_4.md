# Chapter 4: Navigation with Expo Router

## Learning objectives

- Understand **file-based routing** in **`expo-router`**: `_layout.tsx`, dynamic segments, and groups.
- Implement stacks, tabs, and modal presentation.
- Pass parameters between screens with typed routes where possible.
- Configure deep links and a consistent header/back behavior.

---

## 1. Why Expo Router?

**Expo Router** builds on **React Navigation** and maps **files in `app/`** to routes. It reduces boilerplate and aligns with web frameworks that use file-based routing.

Typical structure:

```
app/
  _layout.tsx
  index.tsx
  profile.tsx
  (tabs)/
    _layout.tsx
    home.tsx
    settings.tsx
```

---

## 2. Layouts

Root `_layout.tsx` wraps the whole app (providers, theme, navigation container). Nested `_layout.tsx` files define **stacks** or **tabs** for subtrees.

---

## 3. Moving between screens

- **`href`** with the `Link` component for declarative navigation.
- **`router.push` / `router.replace`** from `expo-router` for imperative navigation.

Params are passed via the path or query; use **`useLocalSearchParams`** to read them.

---

## 4. UX considerations

- Prefer **native stack** animations on each platform.
- Keep **tab** routes shallow; push detail screens onto a **stack** inside a tab when needed.
- Test **Android back** and **iOS swipe-back** gestures.

---

## Summary

- **File-based routes** in `app/` map to URLs and navigation state.
- **Layouts** compose navigators; **dynamic routes** handle IDs.
- Align navigation structure with **mental models** users expect (tabs vs stack).

---

## Practice

1. Create a tab navigator with Home and Profile; add a stack detail screen from Home.
2. Pass an `id` parameter to a detail route and display it on screen.
3. Add a modal screen for “quick create” that dismisses with the platform-appropriate animation.
