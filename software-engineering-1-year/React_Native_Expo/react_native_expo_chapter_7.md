# Chapter 7: Forms, TextInput & Keyboard UX

## Learning objectives

- Build controlled inputs with validation and accessible labels.
- Use **`KeyboardAvoidingView`** / **`keyboardVerticalOffset`** / `react-native-keyboard-controller` patterns.
- Manage focus across fields (`ref`, `onSubmitEditing`, “next” on keyboard).
- Handle secure entry for passwords and platform differences (Android `textContentType`).

---

## 1. Controlled TextInput

```tsx
const [email, setEmail] = useState("");

<TextInput
  value={email}
  onChangeText={setEmail}
  keyboardType="email-address"
  autoCapitalize="none"
  autoCorrect={false}
  accessibilityLabel="Email address"
/>
```

---

## 2. Keyboard and layout

On small screens the keyboard covers the bottom half of the screen. Strategies:

- **`KeyboardAvoidingView`** with behavior `padding` (iOS) / sometimes `height` (Android).
- **`ScrollView`** with `keyboardShouldPersistTaps="handled"` for multi-field forms.
- Consider **`SafeAreaProvider`** + bottom inset padding for tab bars.

---

## 3. Validation

Validate on **blur** for immediate feedback; block submit until rules pass. Show **inline errors** near fields, not only in alerts.

---

## Summary

- **Controlled inputs** + clear **labels** = accessible forms.
- **Keyboard avoidance** is not automatic — test on real devices.
- **Focus management** improves multi-step forms dramatically.

---

## Practice

1. Implement a login form with email, password, and inline errors.
2. Move focus from email to password on “next” from the keyboard.
3. Reproduce a keyboard overlap bug on Android and fix it with your chosen strategy.
