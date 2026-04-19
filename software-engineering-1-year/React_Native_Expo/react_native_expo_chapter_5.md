# Chapter 5: Hooks — State, Effects & Mobile Lifecycle

## Learning objectives

- Use `useState` and `useReducer` for local UI state.
- Apply `useEffect` with correct dependencies; avoid stale closures in event handlers.
- Use `useCallback` / `useMemo` when profiling shows benefit (lists, heavy children).
- Understand AppState (`active`, `background`) for pausing work when the app backgrounds.

---

## 1. State in mobile UIs

Mobile screens remount on navigation; keep **server truth** in a cache or global store when sharing data across tabs (covered more in Chapter 10).

---

## 2. Effects

```tsx
useEffect(() => {
  const sub = AppState.addEventListener("change", (next) => {
    if (next === "background") {
      // pause timers, save draft
    }
  });
  return () => sub.remove();
}, []);
```

Always clean up listeners, intervals, and subscriptions.

---

## 3. Performance hooks

`useCallback` stabilizes function identity for `React.memo` children. `useMemo` caches expensive derived data. **Do not** wrap everything by default — measure first.

---

## Summary

- **Effects** tie your UI to the outside world; **cleanup** prevents leaks.
- **AppState** matters on phones in ways desktop web rarely does.
- Optimize with **`useCallback` / `useMemo`** only when needed.

---

## Practice

1. Build a counter with reset; log mount/unmount in `useEffect`.
2. Subscribe to keyboard events and hide a footer when the keyboard opens.
3. Explain a bug caused by missing dependencies in `useEffect`.
