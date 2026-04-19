# Chapter 8: Assets, Images, Fonts & Splash Screens

## Learning objectives

- Load local images with **`require`** and remote images with **`uri`** + size hints.
- Use **`expo-font`** and `@expo-google-fonts/*` or custom `.ttf` files.
- Configure **splash screen** and **adaptive icons** in `app.json`.
- Understand image caching differences; prefer **`expo-image`** for advanced caching and placeholders.

---

## 1. Images

```tsx
<Image source={require("../assets/icon.png")} style={{ width: 48, height: 48 }} />

<Image
  source={{ uri: "https://example.com/photo.jpg" }}
  style={{ width: "100%", aspectRatio: 16 / 9 }}
  resizeMode="cover"
/>
```

Remote images need explicit dimensions or flex layout with **`aspectRatio`** to avoid layout jumps.

---

## 2. Fonts

Load fonts before rendering UI (splash / gate first paint):

```tsx
import { useFonts } from "expo-font";

const [loaded] = useFonts({ Inter: require("./Inter.ttf") });
if (!loaded) return null;
```

---

## 3. Splash & icons

`app.json` → `expo.icon`, `expo.splash`, Android **adaptiveIcon** foreground/background. Regenerate assets when brand colors change.

---

## Summary

- Always give images **dimensions** or **aspectRatio**.
- **Load fonts** before showing text-heavy screens.
- **Splash and icons** are part of UX — keep them consistent with design system.

---

## Practice

1. Add a custom font and apply it to headings only.
2. Replace default splash with a branded image; test cold start on device.
3. Compare `Image` vs `expo-image` for a grid of remote thumbnails.
