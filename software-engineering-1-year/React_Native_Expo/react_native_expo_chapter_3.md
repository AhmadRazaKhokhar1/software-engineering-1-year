# Chapter 3: TypeScript, Props & Component Architecture

## Learning objectives

- Type React Native components with interfaces for `props` and optional callbacks.
- Split screens into presentational vs container-style components where useful.
- Use `children` and composition patterns familiar from React web.
- Apply strict typing for `StyleProp`, `ViewStyle`, and `TextStyle`.

---

## 1. Typed props

```tsx
import { Pressable, Text } from "react-native";

type PrimaryButtonProps = {
  label: string;
  onPress: () => void;
  disabled?: boolean;
};

export function PrimaryButton({ label, onPress, disabled }: PrimaryButtonProps) {
  return (
    <Pressable onPress={onPress} disabled={disabled} style={({ pressed }) => [{ opacity: pressed ? 0.8 : 1 }]}>
      <Text>{label}</Text>
    </Pressable>
  );
}
```

---

## 2. Style types

Import style types from `react-native`:

```tsx
import type { ViewStyle, TextStyle } from "react-native";

const card: ViewStyle = { padding: 16, borderRadius: 12 };
const title: TextStyle = { fontSize: 18, fontWeight: "700" };
```

---

## 3. Folder structure (small apps)

A common pattern:

```
components/
  ui/
screens/
  HomeScreen.tsx
hooks/
types/
```

Keep **screens** thin; move reusable pieces to **`components/ui`**.

---

## Summary

- TypeScript catches prop mismatches early — essential in growing RN apps.
- **`ViewStyle` / `TextStyle`** clarify what each object styles.
- Composition mirrors React web: small typed components beat one giant screen file.

---

## Practice

1. Create a `Screen` wrapper that applies horizontal padding and safe area insets.
2. Add a `Card` component with `title`, `children`, and optional `footer`.
3. Enable `strict` mode in `tsconfig.json` and fix any new errors in a sample screen.
