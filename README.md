# Reproduction: `setQueryData()` before `useInfiniteQuery()` crashes

Minimal reproduction for [posva/pinia-colada#549](https://github.com/posva/pinia-colada/pull/549).

## The bug

When `queryCache.setQueryData(key, { pages, pageParams })` is called **before** `useInfiniteQuery()` has ever been called for that key, the cache entry is created without the infinite query extension refs (`nextPageParam`, `previousPageParam`). When `useInfiniteQuery()` later mounts for that key, `computePageParams` crashes with:

```
TypeError: Cannot set properties of undefined (setting 'value')
```

## Why this happens

1. `setQueryData()` → `ensureEntry()` → `extend` action fires
2. But `PiniaColadaInfiniteQueryPlugin` isn't registered yet (it registers inside `useInfiniteQuery()`), so the `extend` listener doesn't exist and `entry.ext` stays `{}`
3. Later `useInfiniteQuery()` registers the plugin, then calls `ensureEntry()` — but the entry already exists, so `extend` doesn't fire again
4. `computePageParams` tries `entry.ext.nextPageParam.value = ...` → crash

## Real-world scenario

In our app, optimistic mutations (e.g. favoriting a song, play history tracking) call `setQueryData()` with `InfiniteData`-shaped values from any page. The corresponding `useInfiniteQuery()` only mounts when the user navigates to the library page later.

## Steps to reproduce

```bash
pnpm install
pnpm dev
```

1. Open http://localhost:3000
2. Click **"Pre-populate cache"** (calls `setQueryData` with `{ pages, pageParams }`)
3. Click **"Navigate to List page"** — crashes
