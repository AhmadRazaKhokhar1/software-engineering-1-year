# Chapter 1: React Native, Expo & Your First Mobile App

## Learning objectives

By the end of this chapter you will be able to:

- Explain how React Native differs from web React and from fully native apps.
- Install Node.js, Expo CLI / `npx create-expo-app`, and run a project on a device or simulator.
- Describe the Expo Go workflow versus development builds.
- Navigate an Expo project: `app/` (Expo Router) or `App.tsx`, `app.json`, `package.json`.
- Use core commands: `npx expo start`, tunnel/LAN, and reading Metro bundler errors.

---

## 1. What is React Native?

**React Native** lets you build **native** iOS and Android UIs using **JavaScript/TypeScript** and **React’s component model**. Unlike a WebView wrapper, your UI is backed by **native views** (e.g. `UIView` on iOS, `android.view.View` on Android) through the React Native bridge (or the New Architecture’s JSI).

**Expo** is a toolchain and set of services on top of React Native: faster onboarding, curated native modules, **EAS Build** for cloud builds, and **OTA updates** for JS/asset changes without a full store release (within platform rules).

---

## 2. Environment setup

1. **Node.js (LTS)** — Install from [nodejs.org](https://nodejs.org). Verify: `node -v` and `npm -v`.
2. **Editor** — VS Code is common; install extensions for ESLint and TypeScript.
3. **Expo project** — Prefer the official bootstrap:

```bash
npx create-expo-app@latest MyApp
cd MyApp
npx expo start
```

4. **Device testing** — Install **Expo Go** from the App Store or Google Play; scan the QR code from the terminal (same network, or use tunnel mode if needed).
5. **Simulators** — **Xcode** (iOS Simulator on macOS), **Android Studio** with an AVD for Android Emulator.

---

## 3. Mental model: RN vs web React

| Concept | Web (React) | React Native |
|--------|-------------|----------------|
| Root element | `<div>` | `<View>` |
| Text | `<p>`, `<span>` | Must use `<Text>` for strings |
| Styling | CSS files, often class names | `StyleSheet.create`, flex-first layout |
| Scrolling | `<div>` + overflow | `<ScrollView>` / `FlatList` |

There is **no HTML**; tags like `<View>` compile to native components.

---

## 4. Minimal component example

```tsx
import { StatusBar } from "expo-status-bar";
import { StyleSheet, Text, View } from "react-native";

export default function App() {
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Hello, Expo</Text>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, alignItems: "center", justifyContent: "center" },
  title: { fontSize: 22, fontWeight: "600" },
});
```

---

## 5. Key project files

- **`package.json`** — Scripts (`expo start`), dependencies.
- **`app.json` / `app.config.js`** — App name, slug, icons, splash, plugins, extra config.
- **`App.tsx` or `app/_layout.tsx`** — Entry and navigation (depending on template).

---

## 6. Expo Go vs development build

- **Expo Go** — Quick prototyping; only includes a fixed set of native modules compatible with that Expo SDK version.
- **Development build** — Custom native code and any config plugins; built with EAS or locally.

---

## Summary

- React Native maps React components to **native** UI primitives.
- Expo accelerates **setup**, **native APIs**, and **build/release** workflows.
- Layout is **flex-first**; all visible text lives in **`Text`**.
- Use **`npx expo start`** and Expo Go or simulators for the fastest feedback loop.

---

## Practice

1. Create a new Expo app and change the home screen title and background color.
2. List three differences between React for web and React Native from memory.
3. Run the app on a physical device with Expo Go; note one limitation you observe in the docs.
4. Find `app.json` and change the display name; reload and confirm.
