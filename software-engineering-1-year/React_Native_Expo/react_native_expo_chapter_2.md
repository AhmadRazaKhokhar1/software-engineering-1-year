# Chapter 2: Core Components, StyleSheet & Flexbox Layout

## Learning objectives

- Use `View`, `Text`, `Pressable`, `Image`, and `ScrollView` appropriately.
- Define styles with `StyleSheet.create` and understand when to use inline styles.
- Master Flexbox defaults in React Native (`flexDirection: 'column'`, `flexShrink`, `alignItems`, `justifyContent`).
- Apply safe areas and avoid common layout bugs (text outside `Text`, missing `flex: 1` on scroll roots).

---

## 1. Building blocks

- **`View`** — Layout container; maps to a native view. No text children as raw strings; wrap strings in **`Text`**.
- **`Text`** — All typography; supports nesting for partial styling.
- **`Pressable`** — Touch feedback; prefer over deprecated `TouchableOpacity` for new code (both still appear in legacy tutorials).
- **`Image`** — Use `source={{ uri: '...' }}` or `require('./img.png')` for local assets.
- **`ScrollView`** — Scrollable content; not virtualized — use `FlatList` for long lists (later chapter).

---

## 2. StyleSheet

```tsx
const styles = StyleSheet.create({
  box: {
    padding: 16,
    borderRadius: 8,
    backgroundColor: "#f2f2f2",
  },
});
```

Benefits: validation, slightly better performance than plain objects, centralizes design tokens.

---

## 3. Flexbox in React Native

Defaults differ from CSS on the web:

- **`flexDirection`** defaults to **`column`** (not row).
- **`alignItems`** defaults to **`stretch`**.
- **`flex`** — Often use `flex: 1` on a root `View` to fill the screen.

Typical full-screen centered layout:

```tsx
<View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
  <Text>Centered</Text>
</View>
```

---

## 4. Safe areas

Notches and home indicators require padding. Use **`safe-area-context`** (`SafeAreaView` or `useSafeAreaInsets`) for correct insets on iOS and Android.

---

## Summary

- Containers are **`View`**; strings belong in **`Text`**.
- **`StyleSheet.create`** keeps styles structured and fast to iterate.
- Remember **column** default flex direction and **`flex: 1`** for full-height layouts.
- Combine Flexbox with **safe area** handling on real devices.

---

## Practice

1. Build a card UI: image on top, title, subtitle, and a primary button using `Pressable`.
2. Reproduce a “header + scrollable body + footer” layout using `ScrollView` and `flex`.
3. Explain why `alignItems: 'stretch'` with fixed-height children can look unexpected.
