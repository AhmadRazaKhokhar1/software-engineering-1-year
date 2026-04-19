# Chapter 10: Networking, AsyncStorage & Client State

## Learning objectives

- Fetch JSON with **`fetch`**, handle timeouts and HTTP errors, and parse safely.
- Persist simple key-value data with **`expo-secure-store`** (tokens) vs **`@react-native-async-storage/async-storage`** (preferences).
- Lift shared state with **React Context** or a lightweight store; know when to adopt Zustand / TanStack Query.
- Model loading / error / success states explicitly in UI.

---

## 1. Fetch pattern

```tsx
const [status, setStatus] = useState<"idle" | "loading" | "error" | "success">("idle");

async function load() {
  setStatus("loading");
  try {
    const res = await fetch("https://api.example.com/items");
    if (!res.ok) throw new Error(String(res.status));
    const data = await res.json();
    setStatus("success");
  } catch {
    setStatus("error");
  }
}
```

For production, centralize base URL, auth headers, and retry logic.

---

## 2. Storage choices

| Need | Tool |
|------|------|
| Access / refresh tokens | **SecureStore** |
| Theme, flags, cache | **AsyncStorage** |
| Structured relational data | SQLite / remote DB |

---

## 3. TanStack Query (optional but common)

Caches server state, dedupes requests, and handles refetch-on-focus — excellent fit for mobile.

---

## Summary

- **Explicit async state** beats boolean spaghetti (`loading` + `error` enums).
- **SecureStore** for secrets; **AsyncStorage** for non-sensitive prefs.
- Prefer **battle-tested data libraries** when screens share server data.

---

## Practice

1. Build a list screen that loads from a public JSON API with retry.
2. Persist last successful fetch timestamp in AsyncStorage.
3. Store an API token in SecureStore and attach it to authenticated requests.
