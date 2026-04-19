# Chapter 6: Lists — FlatList, SectionList & Performance

## Learning objectives

- Choose **`FlatList`** for long homogeneous lists; **`SectionList`** for grouped data.
- Provide **`keyExtractor`** and stable **`renderItem`**.
- Use **`getItemLayout`** when row height is fixed (big win for scroll performance).
- Apply **`memo`** on row components and avoid inline heavy functions when lists stutter.

---

## 1. FlatList basics

```tsx
<FlatList
  data={items}
  keyExtractor={(item) => item.id}
  renderItem={({ item }) => <Row title={item.title} />}
/>
```

---

## 2. Performance checklist

- **Virtualization** — Only visible rows mount; still avoid expensive `renderItem` work.
- **`extraData`** — Pass when row content depends on external state not in `item`.
- **Images** — Fixed dimensions or `aspectRatio`; consider **`expo-image`** for caching.
- **Avoid ScrollView** for huge lists — it mounts all children.

---

## 3. Pull to refresh & empty states

Use **`refreshControl`** or the `onRefresh` / `refreshing` props on `FlatList`. Always design an **`ListEmptyComponent`** for zero-data UX.

---

## Summary

- **`FlatList`** is the default tool for scalable lists.
- **Keys**, **memoized rows**, and **`getItemLayout`** fix most jank.
- Pair lists with **refresh** and **empty** states for production quality.

---

## Practice

1. Render 1,000 fake rows with stable keys; enable `getItemLayout` for fixed height.
2. Add pull-to-refresh and a loading skeleton at first fetch.
3. Use React DevTools or why-did-you-render patterns to spot unnecessary row re-renders.
